# Design: jem-ui consumer skills

## Purpose

Three standalone skills that help AI agents effectively use the `@jem-open/jem-ui` design system when building consumer applications. Each skill is independently useful; together they provide comprehensive coverage from individual component usage through full-page recipes.

## Skills overview

| Skill | Purpose | Scope |
|---|---|---|
| `jem-ui-components` | Complete component reference — props, variants, tokens, setup, anti-patterns | Individual components |
| `jem-ui-patterns` | App-level composition guidance for common UI concerns | Component combinations |
| `jem-ui-recipes` | Copy-paste-ready code blocks for common pages and features | Full implementations |

## Independence model

Each skill is self-contained per the jem-agent-skills convention. There are no cross-skill dependencies. Where multiple skills need the same information (e.g. prerequisite checks, component names, validation checklists), that information is repeated inline rather than referenced.

An agent with all three installed gets comprehensive coverage. An agent with just one gets full value from that skill alone.

## Skill 1: jem-ui-components

### Frontmatter

```yaml
name: jem-ui-components
description: Complete reference for using @jem-open/jem-ui components — props, variants, design tokens, setup, and common mistakes to avoid.
```

### Step 1 — Check prerequisites

Verify the consuming project has `@jem-open/jem-ui` installed:

- Check `package.json` for `@jem-open/jem-ui` in dependencies
- Check for `styles.css` import in the app entry point
- Check `tailwind.config.js` for the jem-ui preset

If not set up, provide:

- Install command with peer deps (react, react-dom, tailwindcss)
- `styles.css` import line
- Tailwind preset configuration block

Halt until setup is confirmed.

### Step 2 — Identify needed components

Agent reads the user's request and scans a component catalog table listing every component with: name, category, import path, one-line description.

Component categories:

- **Forms** (14): Button, IconButton, Input, InputField, SearchInput, Checkbox, CheckboxWithLabel, CheckboxCard, RadioGroup, Select, DatePicker, Calendar, Textarea, Switch, Label, Upload
- **Navigation** (5): Accordion, Tabs, Breadcrumb, Pagination, Link
- **Data Display** (7): DataTable, Table, Tag, Avatar, Progress, Divider, Skeleton
- **Feedback** (8): Dialog, Drawer, DropdownMenu, Tooltip, Popover, Alert, EmptyState, Toaster

### Step 3 — Use components correctly

Detailed reference sections organized by category. Each component entry includes:

- Import statement
- Props table (prop name, type, default, description)
- CVA variants table (variant name, options)
- Minimal correct usage example
- `asChild` note where applicable
- Compound component sub-parts where applicable (e.g. Select.Trigger, Select.Content, Select.Item)

### Step 4 — Apply design tokens

Reference tables for:

- **Colors**: Semantic names, CSS variables, Tailwind classes (primary, secondary, error, warning, success, plus extended palette)
- **Spacing**: Full scale from `none` (0px) through `xxxxl` (128px)
- **Typography**: Font families (heading, body), sizes (xxs–9xl), weights (light–black)
- **Border radius**: Full scale from `none` through `full`

### Step 5 — Avoid common mistakes

Explicit anti-patterns with corrections:

| Mistake | Correction |
|---|---|
| Recreating a component that exists in the library | Check the catalog in Step 2 first |
| Using raw Tailwind color values | Use semantic token classes (e.g. `text-primary-surface-default`) |
| Merging classes with template literals | Use `cn()` from `@jem-open/jem-ui` |
| Wrapping polymorphic components in extra elements | Use `asChild` prop |
| Hardcoding token values (hex codes, pixel values) | Use CSS variables or Tailwind classes |

## Skill 2: jem-ui-patterns

### Frontmatter

```yaml
name: jem-ui-patterns
description: Guide for composing @jem-open/jem-ui components into app-level UI patterns — forms, data views, modals, navigation, and feedback.
```

### Step 1 — Check prerequisites

Same setup verification as `jem-ui-components` Step 1 (repeated for self-containment).

### Step 2 — Identify the UI pattern needed

Pattern catalog table:

| Pattern | When to use | Key components |
|---|---|---|
| Form layout | Collecting user input | InputField, Select, Checkbox, RadioGroup, Button, Label |
| Data view | Displaying tabular/list data | DataTable, Pagination, SearchInput, Tag, EmptyState |
| Modal workflow | Confirming actions, multi-step flows | Dialog, Button, form components |
| Drawer panel | Side panels for detail views or forms | Drawer, form components, Button |
| Navigation structure | Page nav, tabs, breadcrumbs | Tabs, Breadcrumb, Accordion, Link |
| Feedback & notifications | Alerts, toasts, empty states | Alert, Toaster (Sonner), EmptyState, Tooltip |

### Step 3 — Apply the pattern

Each pattern gets a dedicated subsection with:

- Component composition order (what wraps what)
- Required props and wiring between components (e.g. form state to InputField error prop)
- Accessibility requirements for the combination (e.g. Dialog needs a title, forms need associated Labels)
- Layout guidance using design tokens (spacing, responsive breakpoints)
- Complete code example showing the components wired together

### Step 4 — Handle state and interactivity

Per-pattern guidance on:

- Where to manage state (controlled vs uncontrolled)
- Event handler wiring between composed components
- Loading and error states across the composition
- Form validation patterns (when to show errors, how to use the `error` prop)

### Step 5 — Validate the composition

Checklist the agent runs through:

- All interactive components have accessible labels
- Spacing uses design tokens, not arbitrary values
- Loading, empty, and error states are handled
- Components use `cn()` for any custom class additions
- No unnecessary wrapper elements (use `asChild` where appropriate)

## Skill 3: jem-ui-recipes

### Frontmatter

```yaml
name: jem-ui-recipes
description: Copy-paste-ready code blocks for common pages and features built with @jem-open/jem-ui — search tables, CRUD forms, settings pages, and more.
```

### Step 1 — Check prerequisites

Same setup verification (repeated for self-containment).

### Step 2 — Select a recipe

Recipe catalog:

| Recipe | Description | Components used |
|---|---|---|
| Search + filter + data table page | Searchable, filterable table with pagination | SearchInput, Select, DataTable, Pagination, Tag, EmptyState |
| CRUD form in a dialog | Create/edit modal with validation and submit | Dialog, InputField, Select, Checkbox, Button, Label |
| Settings page with sections | Grouped form sections with switches and inputs | Tabs, InputField, Switch, Select, Button, Divider |
| Detail drawer | Side panel showing record details with actions | Drawer, Avatar, Tag, Button, Divider, Tooltip |
| Multi-step wizard | Step-by-step flow with progress indication | Dialog, Progress, Button, form components |
| Empty state with CTA | Placeholder for pages/sections with no data | EmptyState, Button |
| Confirmation dialog | Destructive action confirmation | Dialog, Button, Alert |
| File upload form | Upload with preview and validation | Upload, Button, Progress, Alert |

### Step 3 — Apply the recipe

Each recipe includes:

- Complete, working code block (imports through render)
- Inline comments marking where to customize (e.g. `// Replace with your API call`)
- Props pre-filled with realistic placeholder data
- TypeScript types included

### Step 4 — Customize the recipe

Guidance on common modifications:

- Swapping placeholder data for real data sources
- Adding or removing form fields
- Adjusting layout for the app's page structure
- Connecting to the app's state management or API layer

### Step 5 — Verify the result

Same validation checklist as `jem-ui-patterns` Step 5 (repeated for self-containment):

- Accessible labels present
- Design tokens used for spacing
- Loading, empty, and error states handled
- `cn()` used for class additions
- No unnecessary wrappers

## File changes

### Created

- `skills/jem-ui-components/SKILL.md`
- `skills/jem-ui-patterns/SKILL.md`
- `skills/jem-ui-recipes/SKILL.md`

## Key design decisions

| Decision | Rationale |
|---|---|
| Three separate skills, not one monolith | Each serves a distinct use case; keeps context window usage reasonable |
| Self-contained with repeated information | Follows jem-agent-skills convention; no cross-skill dependencies |
| Independently useful, enhanced together | Maximizes open-source adoption; zero-friction for partial installs |
| Step-based structure | Matches existing skill convention (review-fix-loop) |
| Prerequisites repeated in each skill | Self-containment requirement; agent may only have one skill installed |
| Anti-patterns as explicit table | Addresses known agent mistakes directly; easier to scan than prose |
| Component data sourced from jem-ui | Props, variants, tokens extracted from actual source code for accuracy |
