---
name: generate-supervisor-metadata-sql
description: Generate SQL that sets a boolean flag such as is_supervisor inside app_employee.metadata from an employer roster spreadsheet (.xlsx or .csv), merging into the existing JSON without overwriting other keys. Use when asked to flag supervisors, set is_supervisor, or bulk-update employee metadata from a spreadsheet.
---

# generate-supervisor-metadata-sql

Generate SQL that merges `{"is_supervisor": true}` into `app_employee.metadata` for the
employees listed in a roster spreadsheet.

The merge **preserves existing metadata**. It applies the jsonb `||` operator to the current
value, so keys like `is_probation` or `tenure_days` survive untouched — only the target key
is written.

## Prerequisites

- Python 3 with `openpyxl` (`pip install openpyxl`) for `.xlsx` input. CSV input needs
  nothing beyond the standard library.
- Read access to the target database to run the preflight query, and write access to apply.

## Domain facts this skill encodes

- **There is no `is_supervisor` column on `Employee`.** Supervisor status lives in the
  `metadata` JSONField under the `is_supervisor` key, which is how every consumer reads it
  (`verification/services/resolver_registry.py`, and the chatbot's
  `utils/supervisor_utils.is_supervisor`). Writing an `is_supervisor` *column* silently does
  nothing. Regression context: ENG-5684.
- **Write a native JSON boolean `true`, not the string `"true"`.** The resolver accepts
  strings only from `{true, 1, yes, y, t, on}` and fails closed on anything else, so a stray
  `"Yes"` or `"No"` from a CSV import is fragile where a real boolean is not.
- **Rosters carry `employee_no`, not a database id.** The column is often headed `**Pers No`
  — importers prefix required columns with asterisks. `employee_no` is **not globally
  unique**, so always scope by `employer_id`. A single customer may span more than one
  employer row, so confirm which one applies before generating.
- **`metadata` can be SQL `NULL`, JSON `null`, or a stray scalar.** The generated SQL guards
  with `CASE WHEN jsonb_typeof(metadata) = 'object'`. **This guard is load-bearing:** without
  it, `'null'::jsonb || '{"is_supervisor": true}'::jsonb` does not error — it returns the
  *array* `[null, {"is_supervisor": true}]`, silently corrupting the field so that
  `metadata->>'is_supervisor'` reads NULL and the flag never takes effect.

## Step 1 — Inspect the spreadsheet

Print the headers and the job-title breakdown before generating anything:

```bash
python - <<'PY'
import openpyxl, collections
wb = openpyxl.load_workbook("<file>", read_only=True, data_only=True)
ws = wb.worksheets[0]
rows = list(ws.iter_rows(values_only=True))
print("headers:", rows[0])
titles = collections.Counter(str(r[-1] or "").strip() for r in rows[1:] if any(r))
for title, count in titles.most_common():
    print("%6d  %s" % (count, title))
PY
```

## Step 2 — Confirm which job titles count as supervisors

**This is the one judgement call the script must not make silently.** A roster typically
mixes `Supervisor`, `Assistant supervisor`, `Site Manager`, `Camera Room Operator`,
`Floorwalker` and more — only the operator knows which qualify. Show the breakdown from
Step 1 and ask, unless the user has already said. If they say everyone in the sheet, pass
`--all-rows` and skip title filtering entirely.

## Step 3 — Resolve the employer id

If only a name was given:

```sql
SELECT id, name FROM app_employer WHERE name ILIKE '%<name>%';
```

Do not guess an employer id for a bulk write. If it cannot be resolved, ask.

## Step 4 — Generate the SQL

Save the script below as `generate_supervisor_metadata_sql.py` and run it:

```bash
python generate_supervisor_metadata_sql.py \
    --input "<roster.xlsx>" \
    --employer-id <id> \
    --employer-name "<name>"
```

Useful flags: `--all-rows` (ignore titles), `--supervisor-titles "A,B"` (exact list),
`--match-by {employee_no,jem_id,id_number}`, `--metadata-key <key>`, `--include-inactive`,
`--touch-date-updated`, `--output-dir <dir>`.

## Step 5 — Hand over, do not execute

Report the selection count, the title breakdown, and any skipped or duplicate rows. Give the
user the two generated files and the run order below. **Do not run the SQL against a
database yourself.**

## Generated output

| File | Contents |
|------|----------|
| `update_<employer>_<key>.sql` | Preflight query, then the UPDATE in `BEGIN`/`COMMIT` |
| `update_<employer>_<key>_ROLLBACK.sql` | Removes the key again |

**Plain SQL only — no psql meta-commands, no temp tables.** The identifier list is inlined as
a `VALUES` block in the preflight and an `IN` list in the UPDATE, so the output runs in a GUI
query editor (Cloud SQL Studio and similar) as well as in psql. Do not "improve" this with
`\copy` or `CREATE TEMP TABLE`: pooled console connections do not preserve temp tables
between executions, and the inline list costs nothing but file length.

## Run order

**STEP 1** is one read-only query returning six counters. Read them before applying:

| Counter | Meaning |
|---------|---------|
| `matched` | Employees that will be updated. Should equal the selected count. |
| `unmatched` | Identifiers not found under this scope. **A high count means the wrong `employer_id` or payroll-number format — stop.** |
| `ambiguous` | Identifiers resolving to more than one employee; all of them get updated. |
| `already_true` | Already flagged; no-op for these. |
| `already_other` | Carries the key with another value; will be forced to `true`. |
| `non_object` | `metadata` is NULL or a scalar; merges onto `{}`. |

Drill-down templates for the unexpected cases are included beneath the query.

**STEP 2** applies inside `BEGIN`/`COMMIT`. Run the block as one execution so the transaction
stays intact. The reported row count must equal `matched`; if it does not, `ROLLBACK;`
instead of committing.

## Rollback fidelity

The rollback removes the key with `metadata - '<key>'`, which leaves every other key alone.
It is **exact only when STEP 1 reported `already_true = 0` and `already_other = 0`** —
otherwise it also strips the key from employees who legitimately had it, so capture those
from the drill-down before applying. Rows counted as `non_object` return as `{}` rather than
NULL: equivalent to every reader of the field, but not byte-identical.

## The generator script

```python
#!/usr/bin/env python
"""Generate SQL that merges ``{"is_supervisor": true}`` into app_employee.metadata.

Reads an employer-supplied roster (.xlsx or .csv), selects the rows that hold a
supervisor job title, and emits a SQL file containing:

  1. a single preflight query summarising what will happen
  2. the UPDATE, wrapped in BEGIN/COMMIT

plus a companion rollback file that removes the key again.

The UPDATE merges the key into the existing JSON — it never replaces metadata.
Rows whose metadata is NULL or a non-object fall back to '{}' before the merge.

Plain SQL throughout: no psql meta-commands and no temp tables, so the output runs
in Cloud SQL Studio and other GUI query editors as well as in psql.

Usage:
    python generate_supervisor_metadata_sql.py \
        --input "/path/to/roster.xlsx" \
        --employer-id 1234 \
        --employer-name "Acme"
"""
import argparse
import csv
import json
import os
import re
import sys
from collections import Counter, OrderedDict

# Column header aliases, matched after normalisation (lowercase, alphanumerics only).
ID_COLUMN_ALIASES = {
    "employee_no": ["persno", "persnumber", "employeeno", "employeenumber", "empno", "staffno", "payrollno"],
    "jem_id": ["jemid", "employeeid", "id"],
    "id_number": ["idno", "idnumber", "nationalid", "saidnumber"],
}
TITLE_COLUMN_ALIASES = ["jobtitle", "title", "position", "role", "designation", "occupation"]

DEFAULT_TITLE_PATTERN = "supervisor"


def normalise_header(value):
    """'**Pers No' -> 'persno'. Strips the asterisks importers add to required columns."""
    return re.sub(r"[^a-z0-9]", "", str(value or "").lower())


def read_rows(path, sheet=None):
    """Return (headers, rows) where rows are dicts keyed by the raw header text."""
    ext = os.path.splitext(path)[1].lower()
    if ext in (".xlsx", ".xlsm"):
        try:
            import openpyxl
        except ImportError:
            sys.exit("openpyxl is required to read .xlsx files: pip install openpyxl")
        workbook = openpyxl.load_workbook(path, read_only=True, data_only=True)
        worksheet = workbook[sheet] if sheet else workbook.worksheets[0]
        raw = list(worksheet.iter_rows(values_only=True))
        if not raw:
            sys.exit("Sheet is empty: %s" % path)
        headers = ["" if h is None else str(h).strip() for h in raw[0]]
        rows = []
        for values in raw[1:]:
            if not any(v not in (None, "") for v in values):
                continue
            rows.append({headers[i]: values[i] if i < len(values) else None for i in range(len(headers))})
        return headers, rows

    with open(path, "r", encoding="utf-8-sig") as handle:
        reader = csv.DictReader(handle)
        headers = [h.strip() for h in (reader.fieldnames or [])]
        rows = [row for row in reader if any((v or "").strip() for v in row.values())]
        return headers, rows


def resolve_column(headers, explicit, aliases, what):
    if explicit:
        for header in headers:
            if normalise_header(header) == normalise_header(explicit):
                return header
        sys.exit("Column %r not found. Available: %s" % (explicit, ", ".join(headers)))
    for header in headers:
        if normalise_header(header) in aliases:
            return header
    sys.exit("Could not auto-detect the %s column. Available: %s\nPass it explicitly." % (what, ", ".join(headers)))


def cell(row, column):
    value = row.get(column)
    if value is None:
        return ""
    if isinstance(value, float) and value.is_integer():
        value = int(value)
    return str(value).strip()


def sql_literal(value):
    return "'%s'" % str(value).replace("'", "''")


def build_parser():
    parser = argparse.ArgumentParser(description=__doc__, formatter_class=argparse.RawDescriptionHelpFormatter)
    parser.add_argument("--input", required=True, help="Path to the .xlsx or .csv roster")
    parser.add_argument("--sheet", help="Worksheet name (.xlsx only; defaults to the first sheet)")
    parser.add_argument("--employer-id", type=int, help="app_employer.id — required unless --match-by jem_id")
    parser.add_argument("--employer-name", default="", help="Employer name, used in comments and filenames")
    parser.add_argument("--match-by", choices=["employee_no", "jem_id", "id_number"], default="employee_no")
    parser.add_argument("--id-column", help="Override the auto-detected identifier column")
    parser.add_argument("--title-column", help="Override the auto-detected job-title column")
    parser.add_argument("--supervisor-titles",
                        help="Comma-separated exact job titles to flag (case-insensitive). "
                             "Default: any title containing 'supervisor'.")
    parser.add_argument("--title-pattern", default=DEFAULT_TITLE_PATTERN,
                        help="Substring match used when --supervisor-titles is not given (default: supervisor)")
    parser.add_argument("--all-rows", action="store_true",
                        help="Flag every row in the file and ignore job titles entirely")
    parser.add_argument("--metadata-key", default="is_supervisor", help="JSON key to set (default: is_supervisor)")
    parser.add_argument("--include-inactive", action="store_true",
                        help="Drop the smartwage_status = 'active' guard from the WHERE clause")
    parser.add_argument("--touch-date-updated", action="store_true",
                        help="Also set date_updated = NOW(). Off by default — a bulk bump can retrigger "
                             "downstream jobs that poll on date_updated.")
    parser.add_argument("--output-dir", default="local_docs", help="Directory for the generated .sql (default: local_docs)")
    return parser


def main():
    args = build_parser().parse_args()

    if args.match_by != "jem_id" and args.employer_id is None:
        sys.exit("--employer-id is required when matching on %s (employee_no is not globally unique)" % args.match_by)

    headers, rows = read_rows(args.input, args.sheet)
    id_column = resolve_column(headers, args.id_column, ID_COLUMN_ALIASES[args.match_by], args.match_by)

    title_column = None
    if not args.all_rows:
        title_column = resolve_column(headers, args.title_column, TITLE_COLUMN_ALIASES, "job title")

    # --- select the supervisor rows -------------------------------------------------
    exact_titles = None
    if args.supervisor_titles:
        exact_titles = {t.strip().lower() for t in args.supervisor_titles.split(",") if t.strip()}

    title_counts = Counter()
    selected = OrderedDict()   # identifier -> job title (dedupes, preserves file order)
    skipped = []
    duplicates = []

    for index, row in enumerate(rows, start=2):
        identifier = cell(row, id_column)
        title = cell(row, title_column) if title_column else ""
        if title_column:
            title_counts[title] += 1

        if not identifier:
            skipped.append((index, identifier, title, "blank identifier"))
            continue
        if args.match_by == "jem_id" and not identifier.isdigit():
            skipped.append((index, identifier, title, "non-numeric Jem ID"))
            continue

        if args.all_rows:
            matched = True
        elif exact_titles is not None:
            matched = title.lower() in exact_titles
        else:
            matched = args.title_pattern.lower() in title.lower()

        if not matched:
            continue
        if identifier in selected:
            duplicates.append((index, identifier, title))
            continue
        selected[identifier] = title

    if not selected:
        sys.exit("No rows matched the supervisor criteria — nothing to generate.\n"
                 "Titles seen: %s" % ", ".join("%s (%d)" % (t, c) for t, c in title_counts.most_common()))

    # --- output paths ---------------------------------------------------------------
    slug = re.sub(r"[^a-z0-9]+", "_", (args.employer_name or os.path.splitext(os.path.basename(args.input))[0]).lower()).strip("_")
    os.makedirs(args.output_dir, exist_ok=True)
    sql_path = os.path.join(args.output_dir, "update_%s_%s.sql" % (slug, args.metadata_key))
    rollback_path = os.path.join(args.output_dir, "update_%s_%s_ROLLBACK.sql" % (slug, args.metadata_key))

    identifiers = list(selected.keys())
    column = {"employee_no": "employee_no", "jem_id": "id", "id_number": "id_number"}[args.match_by]
    quote = args.match_by != "jem_id"

    def values_list(items):
        return ", ".join(sql_literal(i) if quote else str(i) for i in items)

    # Scope clauses are rendered three ways: bare (single-table UPDATE/SELECT),
    # aliased (the LEFT JOIN preflight) and commented (illustrative queries).
    scope_clauses = []
    if args.employer_id is not None:
        scope_clauses.append("employer_id = %d" % args.employer_id)
    if not args.include_inactive:
        scope_clauses.append("smartwage_status = 'active'")

    def render_scope(prefix="   ", alias=""):
        return "".join("\n%sAND %s%s" % (prefix, alias, clause) for clause in scope_clauses)

    scope_sql = render_scope()                                  # aligns AND under WHERE
    scope_sql_aliased = render_scope(prefix="   ", alias="e.")  # for the joined queries

    merge_sql = (
        "SET metadata = CASE WHEN jsonb_typeof(metadata) = 'object' THEN metadata ELSE '{}'::jsonb END\n"
        "               || %s::jsonb" % sql_literal(json.dumps({args.metadata_key: True}))
    )
    if args.touch_date_updated:
        merge_sql += ",\n    date_updated = NOW()"

    label = args.employer_name or slug

    with open(sql_path, "w") as out:
        out.write("-- Set metadata->>'%s' = true for %s employees\n" % (args.metadata_key, label))
        out.write("-- Generated from %s\n" % os.path.basename(args.input))
        out.write("-- Matching on app_employee.%s%s\n"
                  % (column, " (employer_id = %d)" % args.employer_id if args.employer_id else ""))
        out.write("-- %d employees selected out of %d rows\n" % (len(identifiers), len(rows)))
        if title_column:
            out.write("--\n-- Job title breakdown in the source file:\n")
            for title, count in title_counts.most_common():
                mark = ""
                if not args.all_rows:
                    is_match = (title.lower() in exact_titles if exact_titles is not None
                                else args.title_pattern.lower() in title.lower())
                    mark = "  <-- SELECTED" if is_match else ""
                out.write("--   %-30s %5d%s\n" % (title or "(blank)", count, mark))
        out.write("--\n")
        out.write("-- The UPDATE merges the key into existing metadata with the jsonb || operator,\n")
        out.write("-- so existing keys are preserved. NULL / non-object metadata falls back to '{}'.\n")
        out.write("--\n")
        out.write("-- Plain SQL only - no psql meta-commands, no temp tables. Run STEP 1, read the\n")
        out.write("-- numbers, then run STEP 2. Works in Cloud SQL Studio or any query editor.\n\n")

        # --- preflight ---------------------------------------------------------------
        out.write("-- ============================================================\n")
        out.write("-- STEP 1 - PREFLIGHT (read-only)\n")
        out.write("-- ============================================================\n")
        out.write("-- Expect: matched = %d, unmatched = 0, ambiguous = 0.\n" % len(identifiers))
        out.write("--\n")
        out.write("--   unmatched      - identifier is not in app_employee under this scope.\n")
        out.write("--                    A high count means the wrong employer or number format.\n")
        out.write("--   ambiguous      - identifier resolves to more than one employee; all of\n")
        out.write("--                    them would be updated together.\n")
        out.write("--   already_true   - already flagged; the UPDATE is a no-op for these.\n")
        out.write("--   already_other  - carries the key with some other value; will be forced to\n")
        out.write("--                    true. If this is 0, the rollback file is exact.\n")
        out.write("--   non_object     - metadata is NULL or a scalar; merges onto '{}'.\n")
        out.write("SELECT COUNT(*) FILTER (WHERE e.id IS NOT NULL)                     AS matched,\n")
        out.write("       COUNT(*) FILTER (WHERE e.id IS NULL)                         AS unmatched,\n")
        out.write("       COUNT(*) - COUNT(DISTINCT v.ident)                           AS ambiguous,\n")
        out.write("       COUNT(*) FILTER (WHERE e.metadata ->> '%s' = 'true')         AS already_true,\n"
                  % args.metadata_key)
        out.write("       COUNT(*) FILTER (WHERE e.metadata ? '%s'\n" % args.metadata_key)
        out.write("                          AND e.metadata ->> '%s' IS DISTINCT FROM 'true') AS already_other,\n"
                  % args.metadata_key)
        out.write("       COUNT(*) FILTER (WHERE e.id IS NOT NULL\n")
        out.write("                          AND (e.metadata IS NULL\n")
        out.write("                               OR jsonb_typeof(e.metadata) <> 'object'))     AS non_object\n")
        out.write("  FROM (VALUES\n")
        out.write(",\n".join("    (%s)" % (sql_literal(i) if quote else str(i)) for i in identifiers))
        out.write("\n  ) AS v(ident)\n")
        out.write("  LEFT JOIN app_employee e\n")
        out.write("    ON e.%s = v.ident%s;\n\n" % (column, scope_sql_aliased))

        out.write("-- Drill-downs, if the numbers above are not what you expect. Paste the VALUES\n")
        out.write("-- block from STEP 1 in place of (...) and swap the final filter:\n")
        out.write("--   unmatched      ->  WHERE e.id IS NULL\n")
        out.write("--   ambiguous      ->  GROUP BY v.ident HAVING COUNT(e.id) > 1\n")
        out.write("--   already_other  ->  WHERE e.metadata ? '%s'\n" % args.metadata_key)
        out.write("--                        AND e.metadata ->> '%s' IS DISTINCT FROM 'true'\n\n"
                  % args.metadata_key)

        # --- apply -------------------------------------------------------------------
        out.write("-- ============================================================\n")
        out.write("-- STEP 2 - APPLY\n")
        out.write("-- ============================================================\n")
        out.write("-- Run the whole block as one execution so BEGIN and COMMIT stay together.\n")
        out.write("-- The row count printed by the UPDATE should equal 'matched' from STEP 1.\n\n")
        out.write("BEGIN;\n\n")
        out.write("UPDATE app_employee\n")
        out.write("   %s\n" % merge_sql)
        out.write(" WHERE %s IN (\n" % column)
        out.write(",\n".join("    %s" % (sql_literal(i) if quote else str(i)) for i in identifiers))
        out.write("\n  )%s;\n\n" % scope_sql)
        out.write("-- ROLLBACK; if the count disagrees. Otherwise:\n")
        out.write("COMMIT;\n")

        if duplicates:
            out.write("\n-- Duplicate identifiers in the source file (first occurrence used):\n")
            for line_number, identifier, title in duplicates:
                out.write("--   row %d: %s (%s)\n" % (line_number, identifier, title))
        if skipped:
            out.write("\n-- Skipped rows:\n")
            for line_number, identifier, title, reason in skipped:
                out.write("--   row %d: %r (%s) - %s\n" % (line_number, identifier, title, reason))

    with open(rollback_path, "w") as out:
        out.write("-- ROLLBACK for %s\n" % os.path.basename(sql_path))
        out.write("--\n")
        out.write("-- Removes the '%s' key from the targeted employees.\n" % args.metadata_key)
        out.write("--\n")
        out.write("-- EXACT only if STEP 1 reported already_true = 0 and already_other = 0. If any\n")
        out.write("-- employee carried the key beforehand, this drops it from them too - restore\n")
        out.write("-- those few by hand from the already_* drill-down you ran before applying.\n")
        out.write("--\n")
        out.write("-- Other metadata keys are untouched: the '-' operator removes one key only.\n")
        out.write("--\n")
        out.write("-- One asymmetry: employees counted as non_object in STEP 1 (metadata NULL or a\n")
        out.write("-- scalar) come back as '{}' rather than their original value. Semantically the\n")
        out.write("-- same to every reader of the field, but not byte-identical.\n\n")
        out.write("BEGIN;\n\n")
        out.write("UPDATE app_employee\n")
        out.write("   SET metadata = metadata - '%s'\n" % args.metadata_key)
        out.write(" WHERE %s IN (\n" % column)
        out.write(",\n".join("    %s" % (sql_literal(i) if quote else str(i)) for i in identifiers))
        out.write("\n  )%s\n" % scope_sql)
        out.write("   AND metadata ? '%s';\n\n" % args.metadata_key)
        out.write("-- ROLLBACK; if the count is not what you expect. Otherwise:\n")
        out.write("COMMIT;\n")

    print("Selected %d employees out of %d rows" % (len(identifiers), len(rows)))
    if title_column:
        print("Job titles in file:")
        for title, count in title_counts.most_common():
            is_match = args.all_rows or (title.lower() in exact_titles if exact_titles is not None
                                         else args.title_pattern.lower() in title.lower())
            print("  %-32s %5d%s" % (title or "(blank)", count, "  <-- SELECTED" if is_match else ""))
    if duplicates:
        print("Duplicate identifiers skipped: %d" % len(duplicates))
    if skipped:
        print("Rows skipped: %d" % len(skipped))
    print("SQL:      %s" % sql_path)
    print("Rollback: %s" % rollback_path)


if __name__ == "__main__":
    main()
```
