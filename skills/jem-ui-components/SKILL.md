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

```bash
npm install @jem-open/jem-ui
```

Peer dependencies required: `react@^18 | ^19`, `react-dom@^18 | ^19`, `tailwindcss@^3.4`.

**Check 2: Styles imported**

The app entry point (e.g. `layout.tsx`, `_app.tsx`, or `main.tsx`) must import jem-ui styles:

```typescript
import "@jem-open/jem-ui/styles.css"
```

If missing, add it.

**Check 3: Tailwind preset configured**

`tailwind.config.js` (or `.ts`) must include the jem-ui preset and content path:

```javascript
const jemPreset = require("@jem-open/jem-ui/tailwind-preset");

module.exports = {
  presets: [jemPreset],
  content: [
    "./src/**/*.{ts,tsx}",
    "./node_modules/@jem-open/jem-ui/dist/**/*.{js,mjs}",
  ],
};
```

If missing, add both the preset and the content path.

**Halt** if any check fails and cannot be auto-fixed.

---

## Step 2 — Identify needed components

Before writing any UI code, scan this catalog to find existing components that match your needs. Do not recreate components that already exist in the library.

All components are imported from `@jem-open/jem-ui`:

```typescript
import { ComponentName } from "@jem-open/jem-ui"
```

### Forms

| Component | Description |
|---|---|
| `Button` | Primary action button with 9 variants (default, primary, secondary, destructive, approve, outline, subtle, ghost, link), loading state, and icon slots |
| `IconButton` | Icon-only button with square/circle shapes |
| `Input` | Base text input |
| `InputField` | Input with label, description, helper text, and error state |
| `SearchInput` | Search input with built-in clear button |
| `Checkbox` | Standalone checkbox |
| `CheckboxWithLabel` | Checkbox with label and optional description |
| `CheckboxCard` | Card-style checkbox with label and description |
| `RadioGroup`, `RadioGroupItem` | Radio button group with items |
| `RadioGroupItemWithLabel` | Radio item with label and optional description |
| `RadioGroupCard` | Card-style radio item |
| `Select`, `SelectTrigger`, `SelectContent`, `SelectItem`, `SelectValue` | Composable dropdown select |
| `SelectField` | Select wrapper with label and description |
| `DatePicker` | Single date picker with calendar popover |
| `DateRangePicker` | Date range picker with two-month calendar |
| `Calendar` | Standalone calendar component |
| `Textarea` | Multi-line text input |
| `TextareaField` | Textarea with label, description, helper text, and error state |
| `Switch` | Toggle switch |
| `SwitchWithLabel` | Switch with label and optional description |
| `Label` | Form label (Radix-based, associates with inputs) |
| `Upload` | File upload with progress states (default, uploading, uploaded) |

### Navigation

| Component | Description |
|---|---|
| `Accordion`, `AccordionItem`, `AccordionTrigger`, `AccordionContent` | Collapsible content sections |
| `Tabs`, `TabsList`, `TabsTrigger`, `TabsContent` | Tabbed content with default and line variants |
| `Breadcrumb`, `BreadcrumbList`, `BreadcrumbItem`, `BreadcrumbLink`, `BreadcrumbPage`, `BreadcrumbSeparator` | Navigation breadcrumbs with chevron/slash separator |
| `Pagination`, `PaginationContent`, `PaginationItem`, `PaginationLink`, `PaginationPrevious`, `PaginationNext`, `PaginationEllipsis` | Page navigation controls |
| `Link` | Styled anchor with variant (default, muted) and size (xs, sm, base) options |

### Data Display

| Component | Description |
|---|---|
| `DataTable`, `DataTableColumnHeader` | Full-featured data table with TanStack Table integration, sorting, filtering, pagination |
| `Table`, `TableHeader`, `TableBody`, `TableRow`, `TableHead`, `TableCell`, `TableCaption`, `TableFooter` | Primitive table building blocks |
| `Tag` | Status tag with 13 variants (default, success, processing, pending, failed, drafted, outline, outline-navy, neutral, pink, pink-text, lime, purple) |
| `DismissibleTag` | Tag with dismiss (X) button |
| `CountTag` | Small count badge (pink circle with white number) |
| `Avatar`, `AvatarImage`, `AvatarFallback` | User avatar with image and fallback |
| `AvatarBadge` | Online/status indicator badge for Avatar |
| `AvatarGroup`, `AvatarGroupCount` | Stacked avatar group with overflow count |
| `Progress` | Progress bar |
| `Divider` | Horizontal/vertical separator with label option and 5 style variants |
| `Skeleton` | Loading placeholder with pulse animation |

### Feedback

| Component | Description |
|---|---|
| `Dialog`, `DialogTrigger`, `DialogContent`, `DialogHeader`, `DialogTitle`, `DialogDescription`, `DialogFooter`, `DialogClose` | Modal dialog with close button |
| `Drawer`, `DrawerTrigger`, `DrawerContent`, `DrawerHeader`, `DrawerBody`, `DrawerSection`, `DrawerFooter`, `DrawerTitle`, `DrawerClose` | Side drawer panel (right/left/top/bottom) |
| `DropdownMenu`, `DropdownMenuTrigger`, `DropdownMenuContent`, `DropdownMenuItem`, `DropdownMenuSeparator` | Context/action dropdown menu |
| `DropdownMenuCheckboxItem`, `DropdownMenuRadioGroup`, `DropdownMenuRadioItem` | Selection items within dropdown |
| `DropdownMenuSub`, `DropdownMenuSubTrigger`, `DropdownMenuSubContent` | Nested sub-menus |
| `Tooltip`, `TooltipProvider`, `TooltipTrigger`, `TooltipContent` | Hover tooltip with dark/light variants |
| `Popover`, `PopoverTrigger`, `PopoverContent` | Click-triggered popover |
| `Alert`, `AlertTitle`, `AlertDescription` | Alert message with 5 variants (default, success, warning, destructive, note) and auto icons |
| `EmptyState` | Empty state placeholder with icon, title, description, and action buttons |
| `EmptyStateNotFound`, `EmptyStateNoResults`, `EmptyStateNoData` | Pre-configured empty state variants |
| `Toaster` | Toast notifications — add once in root layout, trigger with `toast()` from sonner |

### Design Tokens

| Component | Description |
|---|---|
| `Icon` | Sized icon wrapper (xs/sm/md/lg/xl) for Lucide icons with accessibility label |
| `PrimaryLogo` | Primary JEM logo with 4 variant/color options and 4 sizes |
| `SecondaryLogoRound` | Round secondary logo with 2 variants and 4 sizes |
| `SecondaryLogoSquare` | Square secondary logo with 2 variants and 4 sizes |

### Utilities

| Export | Description |
|---|---|
| `cn` | Class name merger (clsx + tailwind-merge). **Use this for ALL class composition.** Never use template literals to combine Tailwind classes. |
