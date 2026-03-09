# jem-ui Consumer Skills Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Create three standalone skills that help AI agents effectively use the `@jem-open/jem-ui` design system when building consumer applications.

**Architecture:** Three independent SKILL.md files following the jem-agent-skills convention. Each skill is self-contained with repeated prerequisite checks and validation checklists. Component data is sourced from the jem-ui codebase and embedded directly in each skill.

**Tech Stack:** Markdown with YAML frontmatter (skill content format)

---

### Task 1: Create jem-ui-components skill directory and frontmatter

**Files:**
- Create: `skills/jem-ui-components/SKILL.md`

**Step 1: Create the skill directory**

```bash
mkdir -p skills/jem-ui-components
```

**Step 2: Write the SKILL.md with frontmatter and Step 1 (prerequisites)**

Write `skills/jem-ui-components/SKILL.md` with:

```markdown
---
name: jem-ui-components
description: Complete reference for using @jem-open/jem-ui components — props, variants, design tokens, setup, and common mistakes to avoid.
---

# jem-ui-components

Complete reference for AI agents consuming `@jem-open/jem-ui` in application projects. Covers every component's props, variants, and design tokens so you use the library correctly instead of recreating what already exists.

## Step 1 — Check prerequisites

Verify the consuming project is set up to use jem-ui.

**Check 1: Package installed**

Look for `@jem-open/jem-ui` in `package.json` dependencies or devDependencies.

If not found, install it:

\`\`\`bash
npm install @jem-open/jem-ui
\`\`\`

Peer dependencies required: `react@^18 | ^19`, `react-dom@^18 | ^19`, `tailwindcss@^3.4`.

**Check 2: Styles imported**

The app entry point (e.g. `layout.tsx`, `_app.tsx`, or `main.tsx`) must import jem-ui styles:

\`\`\`typescript
import "@jem-open/jem-ui/styles.css"
\`\`\`

If missing, add it.

**Check 3: Tailwind preset configured**

`tailwind.config.js` (or `.ts`) must include the jem-ui preset and content path:

\`\`\`javascript
const jemPreset = require("@jem-open/jem-ui/tailwind-preset");

module.exports = {
  presets: [jemPreset],
  content: [
    "./src/**/*.{ts,tsx}",
    "./node_modules/@jem-open/jem-ui/dist/**/*.{js,mjs}",
  ],
};
\`\`\`

If missing, add both the preset and the content path.

**Halt** if any check fails and cannot be auto-fixed.
```

**Step 3: Verify skill discovery**

```bash
npx skills add . --list
```

Expected: `jem-ui-components` appears in the list.

**Step 4: Commit**

```bash
git add skills/jem-ui-components/SKILL.md
git commit -m "feat: add jem-ui-components skill with prerequisites step"
```

---

### Task 2: Add component catalog (Step 2) to jem-ui-components

**Files:**
- Modify: `skills/jem-ui-components/SKILL.md`

**Step 1: Append Step 2 — Identify needed components**

Add the full component catalog table after Step 1. This is the scannable index agents use to find the right component.

The catalog table has columns: Component, Category, Import, Description.

**Forms components (16 exports):**

| Component | Import | Description |
|---|---|---|
| Button | `import { Button } from "@jem-open/jem-ui"` | Primary action button with variants, icons, and loading state |
| IconButton | `import { IconButton } from "@jem-open/jem-ui"` | Icon-only button with square/circle shapes |
| Input | `import { Input } from "@jem-open/jem-ui"` | Base text input |
| InputField | `import { InputField } from "@jem-open/jem-ui"` | Input with label, description, helper text, and error state |
| SearchInput | `import { SearchInput } from "@jem-open/jem-ui"` | Search input with clear button |
| Checkbox | `import { Checkbox } from "@jem-open/jem-ui"` | Standalone checkbox |
| CheckboxWithLabel | `import { CheckboxWithLabel } from "@jem-open/jem-ui"` | Checkbox with label and optional description |
| CheckboxCard | `import { CheckboxCard } from "@jem-open/jem-ui"` | Card-style checkbox with label and description |
| RadioGroup, RadioGroupItem | `import { RadioGroup, RadioGroupItem } from "@jem-open/jem-ui"` | Radio button group |
| Select, SelectTrigger, SelectContent, SelectItem, SelectValue | `import { Select, SelectTrigger, SelectContent, SelectItem, SelectValue } from "@jem-open/jem-ui"` | Composable dropdown select |
| DatePicker | `import { DatePicker } from "@jem-open/jem-ui"` | Single date picker with calendar popover |
| DateRangePicker | `import { DateRangePicker } from "@jem-open/jem-ui"` | Date range picker with two-month calendar |
| Calendar | `import { Calendar } from "@jem-open/jem-ui"` | Standalone calendar (used inside DatePicker) |
| Textarea | `import { Textarea } from "@jem-open/jem-ui"` | Multi-line text input |
| Switch | `import { Switch } from "@jem-open/jem-ui"` | Toggle switch |
| Label | `import { Label } from "@jem-open/jem-ui"` | Form label (Radix-based, associates with inputs) |
| Upload | `import { Upload } from "@jem-open/jem-ui"` | File upload with progress and states |

**Navigation components (5 groups):**

| Component | Import | Description |
|---|---|---|
| Accordion, AccordionItem, AccordionTrigger, AccordionContent | `import { Accordion, AccordionItem, AccordionTrigger, AccordionContent } from "@jem-open/jem-ui"` | Collapsible content sections |
| Tabs, TabsList, TabsTrigger, TabsContent | `import { Tabs, TabsList, TabsTrigger, TabsContent } from "@jem-open/jem-ui"` | Tabbed content with default and line variants |
| Breadcrumb, BreadcrumbList, BreadcrumbItem, BreadcrumbLink, BreadcrumbPage, BreadcrumbSeparator | `import { Breadcrumb, BreadcrumbList, BreadcrumbItem, BreadcrumbLink, BreadcrumbPage, BreadcrumbSeparator } from "@jem-open/jem-ui"` | Navigation breadcrumbs |
| Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationPrevious, PaginationNext | `import { Pagination, PaginationContent, PaginationItem, PaginationLink, PaginationPrevious, PaginationNext } from "@jem-open/jem-ui"` | Page navigation |
| Link | `import { Link } from "@jem-open/jem-ui"` | Styled anchor with variant and size options |

**Data Display components (7 groups):**

| Component | Import | Description |
|---|---|---|
| DataTable, DataTableColumnHeader | `import { DataTable, DataTableColumnHeader } from "@jem-open/jem-ui"` | Full-featured data table with TanStack integration |
| Table, TableHeader, TableBody, TableRow, TableHead, TableCell | `import { Table, TableHeader, TableBody, TableRow, TableHead, TableCell } from "@jem-open/jem-ui"` | Primitive table elements |
| Tag, DismissibleTag, CountTag | `import { Tag, DismissibleTag, CountTag } from "@jem-open/jem-ui"` | Status tags with 13 variants |
| Avatar, AvatarImage, AvatarFallback | `import { Avatar, AvatarImage, AvatarFallback } from "@jem-open/jem-ui"` | User avatar with image and fallback |
| Progress | `import { Progress } from "@jem-open/jem-ui"` | Progress bar |
| Divider | `import { Divider } from "@jem-open/jem-ui"` | Horizontal/vertical separator with label option |
| Skeleton | `import { Skeleton } from "@jem-open/jem-ui"` | Loading placeholder |

**Feedback components (8 groups):**

| Component | Import | Description |
|---|---|---|
| Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogDescription, DialogFooter, DialogClose | `import { Dialog, DialogTrigger, DialogContent, DialogHeader, DialogTitle, DialogDescription, DialogFooter, DialogClose } from "@jem-open/jem-ui"` | Modal dialog |
| Drawer, DrawerTrigger, DrawerContent, DrawerHeader, DrawerBody, DrawerFooter, DrawerTitle, DrawerClose | `import { Drawer, DrawerTrigger, DrawerContent, DrawerHeader, DrawerBody, DrawerFooter, DrawerTitle, DrawerClose } from "@jem-open/jem-ui"` | Side drawer panel (left/right/top/bottom) |
| DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem, DropdownMenuSeparator | `import { DropdownMenu, DropdownMenuTrigger, DropdownMenuContent, DropdownMenuItem, DropdownMenuSeparator } from "@jem-open/jem-ui"` | Context menu / action menu |
| Tooltip, TooltipProvider, TooltipTrigger, TooltipContent | `import { Tooltip, TooltipProvider, TooltipTrigger, TooltipContent } from "@jem-open/jem-ui"` | Hover tooltip (dark/light variants) |
| Popover, PopoverTrigger, PopoverContent | `import { Popover, PopoverTrigger, PopoverContent } from "@jem-open/jem-ui"` | Click-triggered popover |
| Alert, AlertTitle, AlertDescription | `import { Alert, AlertTitle, AlertDescription } from "@jem-open/jem-ui"` | Alert message with variant icons |
| EmptyState | `import { EmptyState } from "@jem-open/jem-ui"` | Empty state placeholder with actions |
| Toaster | `import { Toaster } from "@jem-open/jem-ui"` | Toast notifications (add once in layout) |

**Design Tokens (4 exports):**

| Component | Import | Description |
|---|---|---|
| Icon | `import { Icon } from "@jem-open/jem-ui"` | Sized icon wrapper for Lucide icons |
| PrimaryLogo | `import { PrimaryLogo } from "@jem-open/jem-ui"` | Primary JEM logo with variant/size |
| SecondaryLogoRound | `import { SecondaryLogoRound } from "@jem-open/jem-ui"` | Round secondary logo |
| SecondaryLogoSquare | `import { SecondaryLogoSquare } from "@jem-open/jem-ui"` | Square secondary logo |

**Utilities:**

| Export | Import | Description |
|---|---|---|
| cn | `import { cn } from "@jem-open/jem-ui"` | Class name merger (clsx + tailwind-merge). Use this for ALL class composition. |

**Step 2: Commit**

```bash
git add skills/jem-ui-components/SKILL.md
git commit -m "feat(jem-ui-components): add component catalog step"
```

---

### Task 3: Add component reference sections (Step 3) to jem-ui-components — Forms

**Files:**
- Modify: `skills/jem-ui-components/SKILL.md`

**Step 1: Append Step 3 — Use components correctly, starting with Forms subsection**

For each form component, document: props table, CVA variants table, minimal usage example, and notes (asChild, compound parts, etc.).

Components to document in this task:
- Button / IconButton (variants: default, primary, secondary, destructive, approve, outline, subtle, ghost, link; sizes: default, xs, sm, small, medium, lg, large, icon, icon-xs, icon-sm, icon-lg; props: leftIcon, rightIcon, loading, asChild)
- Input / InputField / SearchInput (InputField props: label, description, helperText, error, icon, button; SearchInput props: onClear)
- Checkbox / CheckboxWithLabel / CheckboxCard (label, description props)
- RadioGroup / RadioGroupItem / RadioGroupItemWithLabel / RadioGroupCard
- Select compound components (Select, SelectTrigger, SelectContent, SelectItem, SelectValue, SelectField with label/description)
- DatePicker / DateRangePicker (date, onDateChange, placeholder, disabled, label props)
- Calendar
- Textarea / TextareaField (label, description, helperText, error props)
- Switch / SwitchWithLabel (label, description props)
- Label
- Upload (state: default/uploading/uploaded, progress, fileName, title, description, maxSize, onSelectFile, onRemoveFile, onSubmit)

Include exact CVA variant values and defaults sourced from the jem-ui codebase. Include one minimal usage example per component showing the most common usage pattern.

**Step 2: Commit**

```bash
git add skills/jem-ui-components/SKILL.md
git commit -m "feat(jem-ui-components): add forms component reference"
```

---

### Task 4: Add component reference sections (Step 3) to jem-ui-components — Navigation, Data Display, Feedback, Design Tokens

**Files:**
- Modify: `skills/jem-ui-components/SKILL.md`

**Step 1: Append Navigation subsection**

Document:
- Accordion compound (AccordionItem, AccordionTrigger, AccordionContent)
- Tabs compound (TabsList variants: default/line; TabsTrigger variants: default/line; TabsContent)
- Breadcrumb compound (BreadcrumbSeparator variant: chevron/slash; BreadcrumbLink asChild support)
- Pagination compound (PaginationLink isActive prop)
- Link (variants: default/muted; sizes: xs/sm/base)

**Step 2: Append Data Display subsection**

Document:
- DataTable (columns, data, filterColumn, filterPlaceholder, showPagination, showToolbar, showRowsSelected, toolbarChildren props; DataTableColumnHeader for sortable columns)
- Table compound (semantic HTML table parts)
- Tag (13 variants: default, success, processing, pending, failed, drafted, outline, outline-navy, neutral, pink, pink-text, lime, purple; DismissibleTag onDismiss; CountTag)
- Avatar (sizes: sm/md/lg; AvatarFallback; AvatarBadge; AvatarGroup; AvatarGroupCount)
- Progress (value prop)
- Divider (orientation: horizontal/vertical; variant: default/subtle/strong/primary/secondary; spacing: none/sm/md/lg; label prop)
- Skeleton

**Step 3: Append Feedback subsection**

Document:
- Dialog compound (DialogContent showCloseButton; DialogTitle variant: default/error; DialogFooter; DialogContact)
- Drawer compound (direction: right/left/top/bottom; DrawerHeader showCloseButton; DrawerBody; DrawerSection; DrawerFooter)
- DropdownMenu compound (DropdownMenuItem variant: default/destructive; inset; CheckboxItem; RadioGroup/RadioItem; Sub menus)
- Tooltip (TooltipContent variant: dark/light; needs TooltipProvider wrapper)
- Popover compound (PopoverContent align, sideOffset)
- Alert (variant: default/success/warning/destructive/note; hideIcon; automatic icon per variant)
- EmptyState (variant: default/card/bordered; size: sm/md/lg; icon types: folder/alert/search/file/inbox/users; primaryAction/secondaryAction; pre-configured variants: EmptyStateNotFound, EmptyStateNoResults, EmptyStateNoData)
- Toaster (add once in root layout; toast icons auto-configured)

**Step 4: Append Design Tokens subsection**

Document:
- Icon (size: xs/sm/md/lg/xl; icon prop takes LucideIcon; label for accessibility; list available re-exported icons by category)
- PrimaryLogo (variant: bg-pink/bg-white/pink/white; size: sm/md/lg/xl)
- SecondaryLogoRound (variant: bg-pink/bg-white; size: sm/md/lg/xl)
- SecondaryLogoSquare (variant: bg-pink/bg-white; size: sm/md/lg/xl)

**Step 5: Commit**

```bash
git add skills/jem-ui-components/SKILL.md
git commit -m "feat(jem-ui-components): add navigation, data display, feedback, and design token references"
```

---

### Task 5: Add design tokens reference (Step 4) and anti-patterns (Step 5) to jem-ui-components

**Files:**
- Modify: `skills/jem-ui-components/SKILL.md`

**Step 1: Append Step 4 — Apply design tokens**

Include complete token reference tables:

**Colors — Semantic tokens (use these, not raw values):**

| Token category | CSS variable pattern | Tailwind class pattern | Values |
|---|---|---|---|
| Primary surface | `--primary-surface-{default,lighter,subtle,darker}` | `bg-primary-surface-{default,lighter,subtle,darker}` | Navy shades |
| Primary border | `--primary-border-{default,lighter,subtle,darker}` | `border-primary-border-{default,lighter,subtle,darker}` | Navy shades |
| Primary text | `--primary-text-label` | `text-primary-text-label` | Navy |
| Secondary surface | `--secondary-surface-{default,lighter,subtle,darker}` | `bg-secondary-surface-{default,lighter,subtle,darker}` | Pink shades |
| Secondary border | `--secondary-border-{default,lighter,subtle,darker}` | `border-secondary-border-{default,lighter,subtle,darker}` | Pink shades |
| Secondary text | `--secondary-text-label` | `text-secondary-text-label` | Pink |
| Error/Warning/Success | Same pattern with `error-`, `warning-`, `success-` | Same pattern | Red/Orange/Green |
| Greyscale surface | `--greyscale-surface-{default,subtle,disabled}` | `bg-greyscale-surface-{default,subtle,disabled}` | Neutral shades |
| Greyscale border | `--greyscale-border-{default,disabled,darker}` | `border-greyscale-border-{default,disabled,darker}` | Neutral shades |
| Greyscale text | `--greyscale-text-{title,body,subtitle,caption,negative,disabled}` | `text-greyscale-text-{title,body,subtitle,caption,negative,disabled}` | Navy to neutral |

**Colors — Base palette (use sparingly, prefer semantic tokens):**

| Palette | CSS variable | Tailwind class | Shades |
|---|---|---|---|
| Navy | `--navy-{50-900}` | `bg-navy-{50-900}`, `text-navy-{50-900}` | 50, 100, 200, 300, 400, 500, 600, 700, 800, 900 |
| Pink | `--pink-{50-900}` | `bg-pink-{50-900}`, `text-pink-{50-900}` | Same |
| Lime, Purple, Violet, Blue, Green, Orange, Yellow, Red | Same pattern | Same pattern | Same |
| Neutral | `--neutral-{50-900,white,cream,black}` | Same pattern | 50-900 + white, cream, black |

**Spacing:**

| Token | CSS variable | Tailwind class | Value |
|---|---|---|---|
| none | `--spacing-none` | `p-none`, `m-none`, `gap-none` | 0px |
| xxxxs | `--spacing-xxxxs` | `p-xxxxs`, `m-xxxxs`, `gap-xxxxs` | 2px |
| xxxs | `--spacing-xxxs` | `p-xxxs`, `m-xxxs`, `gap-xxxs` | 4px |
| xxs | `--spacing-xxs` | `p-xxs`, `m-xxs`, `gap-xxs` | 8px |
| xs | `--spacing-xs` | `p-xs`, `m-xs`, `gap-xs` | 12px |
| sm | `--spacing-sm` | `p-sm`, `m-sm`, `gap-sm` | 16px |
| md | `--spacing-md` | `p-md`, `m-md`, `gap-md` | 24px |
| lg | `--spacing-lg` | `p-lg`, `m-lg`, `gap-lg` | 32px |
| xl | `--spacing-xl` | `p-xl`, `m-xl`, `gap-xl` | 48px |
| xxl | `--spacing-xxl` | `p-xxl`, `m-xxl`, `gap-xxl` | 64px |
| xxxl | `--spacing-xxxl` | `p-xxxl`, `m-xxxl`, `gap-xxxl` | 96px |
| xxxxl | `--spacing-xxxxl` | `p-xxxxl`, `m-xxxxl`, `gap-xxxxl` | 128px |

**Typography:**

| Property | Tokens | Values |
|---|---|---|
| Font family | `font-heading`, `font-body` | From CSS variables `--font-family-heading`, `--font-family-body` |
| Font size | `text-xxs` through `text-9xl` | 10px, 12px, 14px, 16px, 18px, 20px, 24px, 30px, 36px, 48px, 60px, 72px, 96px, 128px |
| Font weight | `font-light` through `font-black` | 300, 400, 500, 600, 700, 800, 900 |

**Border radius:**

| Token | Tailwind class | Value |
|---|---|---|
| none | `rounded-none` | 0px |
| xs | `rounded-xs` | 2px |
| sm | `rounded-sm` | 4px |
| md | `rounded-md` | 6px |
| lg | `rounded-lg` | 8px |
| xl | `rounded-xl` | 12px |
| 2xl | `rounded-2xl` | 16px |
| 3xl | `rounded-3xl` | 24px |
| 4xl | `rounded-4xl` | 32px |
| full | `rounded-full` | 100px |

**Step 2: Append Step 5 — Avoid common mistakes**

| Mistake | Why it's wrong | Correction |
|---|---|---|
| Building a custom button component | jem-ui already has Button with 9 variants and loading state | Check the catalog in Step 2 — the component likely exists |
| Using raw hex colors like `bg-[#062133]` | Bypasses the design system; breaks if tokens change | Use semantic tokens: `bg-primary-surface-default` |
| Merging classes with template literals: `` `${base} ${conditional}` `` | Tailwind classes can conflict; template literals don't resolve conflicts | Use `cn()`: `cn("base-class", conditional && "conditional-class")` |
| Wrapping a Button in an anchor: `<a><Button>Click</Button></a>` | Creates nested interactive elements (accessibility violation) | Use `asChild`: `<Button asChild><a href="/path">Click</a></Button>` |
| Writing `style={{ padding: '16px' }}` | Bypasses Tailwind; inconsistent with design tokens | Use spacing tokens: `className="p-sm"` (16px) |
| Hardcoding `#ff697f` for pink | Raw hex breaks if the palette changes | Use `text-pink-500` or semantic `text-secondary-text-label` |
| Forgetting `TooltipProvider` wrapper | Tooltips won't render without a provider | Wrap your app or layout in `<TooltipProvider>` |
| Adding `Toaster` in every page | Creates duplicate toasts | Add `<Toaster />` once in the root layout |
| Using `<div onClick>` for clickable elements | Not keyboard accessible, no focus management | Use `<Button variant="ghost">` or the appropriate interactive component |

**Step 3: Commit**

```bash
git add skills/jem-ui-components/SKILL.md
git commit -m "feat(jem-ui-components): add design tokens reference and anti-patterns"
```

**Step 4: Verify final skill discovery**

```bash
npx skills add . --list
```

Expected: `jem-ui-components` appears with its description.

---

### Task 6: Create jem-ui-patterns skill

**Files:**
- Create: `skills/jem-ui-patterns/SKILL.md`

**Step 1: Create the skill directory and write the complete SKILL.md**

```bash
mkdir -p skills/jem-ui-patterns
```

Write the full `skills/jem-ui-patterns/SKILL.md` with:

- Frontmatter: name `jem-ui-patterns`, description about composing jem-ui components for app-level UI patterns
- Step 1 — Check prerequisites (same checks as jem-ui-components: package installed, styles imported, tailwind preset configured; halt if not set up)
- Step 2 — Identify the UI pattern needed (pattern catalog table with: Form layout, Data view, Modal workflow, Drawer panel, Navigation structure, Feedback & notifications — each listing when to use and key components)
- Step 3 — Apply the pattern (dedicated subsection per pattern with: composition order, required props/wiring, accessibility requirements, layout guidance with tokens, complete code example)
  - **Form layout pattern**: InputField/Select/Checkbox/RadioGroup composed in a form element, Label associations, error state wiring, Button submit, vertical spacing with `gap-md`
  - **Data view pattern**: DataTable with columns/data, SearchInput for filtering, Tag for status cells, Pagination (or built-in DataTable pagination), EmptyState when no results
  - **Modal workflow pattern**: Dialog wrapping form components, DialogHeader/DialogTitle/DialogDescription for context, DialogFooter with cancel/submit buttons, DialogClose wiring
  - **Drawer panel pattern**: Drawer with direction="right", DrawerHeader with title, DrawerBody for content, DrawerFooter with actions, form components inside DrawerBody
  - **Navigation structure pattern**: Tabs with TabsList/TabsTrigger/TabsContent for page sections, Breadcrumb for hierarchical navigation, Accordion for collapsible sections
  - **Feedback & notifications pattern**: Alert for inline messages (variant per severity), Toaster for transient notifications (toast() function from sonner), EmptyState for no-data states, Tooltip for icon/button hints
- Step 4 — Handle state and interactivity (per-pattern guidance on controlled vs uncontrolled, event wiring, loading/error states, form validation)
- Step 5 — Validate the composition (checklist: accessible labels, token-based spacing, state handling, cn() usage, no unnecessary wrappers)

Each code example in Step 3 should be complete and runnable — imports through JSX return — using realistic prop values.

**Step 2: Verify skill discovery**

```bash
npx skills add . --list
```

Expected: `jem-ui-patterns` appears in the list.

**Step 3: Commit**

```bash
git add skills/jem-ui-patterns/SKILL.md
git commit -m "feat: add jem-ui-patterns skill for component composition guidance"
```

---

### Task 7: Create jem-ui-recipes skill

**Files:**
- Create: `skills/jem-ui-recipes/SKILL.md`

**Step 1: Create the skill directory and write the complete SKILL.md**

```bash
mkdir -p skills/jem-ui-recipes
```

Write the full `skills/jem-ui-recipes/SKILL.md` with:

- Frontmatter: name `jem-ui-recipes`, description about copy-paste-ready code blocks for common pages and features
- Step 1 — Check prerequisites (same checks as other skills; halt if not set up)
- Step 2 — Select a recipe (recipe catalog table with all 8 recipes, their descriptions, and components used)
- Step 3 — Apply the recipe (each recipe as a subsection with complete, working code)

**Recipes to include:**

1. **Search + filter + data table page**: Full page component with SearchInput at top, Select filters, DataTable with typed columns (use a realistic example like a user list or order list), Pagination, EmptyState fallback. Include column definitions with DataTableColumnHeader for sorting, Tag for status column. Mark customization points with `// TODO: Replace with your data type` comments.

2. **CRUD form in a dialog**: Dialog triggered by a Button, form inside DialogContent with InputField, Select, Checkbox, submit Button in DialogFooter. Show controlled form state with useState. Mark `// TODO: Replace with your API call` on submit.

3. **Settings page with sections**: Tabs for settings categories, each TabsContent containing grouped form fields (InputField, Switch, Select) separated by Divider. Button at bottom of each section for save.

4. **Detail drawer**: Drawer (direction="right") with DrawerHeader showing title, DrawerBody with Avatar, descriptive fields using Divider separators, Tag for status, DrawerFooter with action Buttons and Tooltip hints.

5. **Multi-step wizard**: Dialog with Progress bar in DialogHeader, conditional step content (3 steps), Button navigation (Back/Next/Submit), step validation before advancing.

6. **Empty state with CTA**: EmptyState with icon, title, description, primaryAction (Button), secondaryAction (Link). Show both `default` and `card` variant examples.

7. **Confirmation dialog**: Dialog with DialogTitle variant="error", warning message in DialogDescription, destructive Button and cancel Button in DialogFooter.

8. **File upload form**: Upload component with state management (default → uploading → uploaded), Progress during upload, Alert for validation errors, Button to submit.

- Step 4 — Customize the recipe (guidance on swapping data sources, adjusting fields, connecting to state management/API)
- Step 5 — Verify the result (same validation checklist as jem-ui-patterns)

All code blocks must be TypeScript, include imports, use realistic placeholder data, and have inline comments marking customization points.

**Step 2: Verify skill discovery**

```bash
npx skills add . --list
```

Expected: `jem-ui-recipes` appears in the list.

**Step 3: Commit**

```bash
git add skills/jem-ui-recipes/SKILL.md
git commit -m "feat: add jem-ui-recipes skill with 8 copy-paste-ready recipes"
```

---

### Task 8: Update README and verify all skills

**Files:**
- Modify: `README.md`

**Step 1: Add the three new skills to the README skill catalog**

Add entries for `jem-ui-components`, `jem-ui-patterns`, and `jem-ui-recipes` to the skills table in README.md, following the existing format.

**Step 2: Final verification**

```bash
npx skills add . --list
```

Expected: All four skills appear: `review-fix-loop`, `jem-ui-components`, `jem-ui-patterns`, `jem-ui-recipes`.

**Step 3: Commit**

```bash
git add README.md
git commit -m "docs: add jem-ui skills to README catalog"
```
