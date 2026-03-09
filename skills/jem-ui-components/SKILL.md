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

---

## Step 3 — Use components correctly

Detailed reference for each component. Organized by category.

### Forms

#### Button

```typescript
import { Button } from "@jem-open/jem-ui"
```

**Props:** extends `React.ComponentProps<"button">`

| Prop | Type | Default | Description |
|---|---|---|---|
| `variant` | `"default" \| "primary" \| "secondary" \| "destructive" \| "approve" \| "outline" \| "subtle" \| "ghost" \| "link"` | `"default"` | Visual style |
| `size` | `"default" \| "xs" \| "sm" \| "small" \| "medium" \| "lg" \| "large" \| "icon" \| "icon-xs" \| "icon-sm" \| "icon-lg"` | `"default"` | Button size |
| `leftIcon` | `React.ReactNode` | — | Icon before label |
| `rightIcon` | `React.ReactNode` | — | Icon after label |
| `loading` | `boolean` | `false` | Shows loading spinner, disables button |
| `asChild` | `boolean` | `false` | Render as child element (use with links) |

**Example:**

```tsx
<Button variant="primary" size="medium" leftIcon={<Plus />}>
  Add item
</Button>
```

**asChild example (Button as link):**

```tsx
<Button asChild variant="outline">
  <a href="/settings">Settings</a>
</Button>
```

#### IconButton

```typescript
import { IconButton } from "@jem-open/jem-ui"
```

**Props:** extends `React.ComponentProps<"button">`

| Prop | Type | Default | Description |
|---|---|---|---|
| `size` | `"default" \| "small" \| "medium" \| "large"` | `"default"` | Button size |
| `shape` | `"square" \| "circle"` | `"square"` | Button shape |
| `icon` | `React.ReactNode` | — | Icon to display |

**Example:**

```tsx
<IconButton icon={<Trash2 />} shape="circle" aria-label="Delete item" />
```

Note: Always provide `aria-label` for accessibility since there is no visible text.

#### Input

```typescript
import { Input } from "@jem-open/jem-ui"
```

**Props:** extends `React.ComponentProps<"input">`

No additional props beyond standard HTML input attributes.

**Example:**

```tsx
<Input type="email" placeholder="Enter email" />
```

#### InputField

```typescript
import { InputField } from "@jem-open/jem-ui"
```

**Props:** extends Input props

| Prop | Type | Default | Description |
|---|---|---|---|
| `label` | `string` | — | Label text above input |
| `description` | `string` | — | Description below label |
| `helperText` | `string` | — | Helper text below input |
| `error` | `boolean` | `false` | Error state (red border, helper text turns red) |
| `icon` | `React.ReactNode` | — | Icon inside input |
| `button` | `React.ReactNode` | — | Button element inside input |

**Example:**

```tsx
<InputField
  label="Email"
  description="We'll never share your email"
  helperText={errors.email}
  error={!!errors.email}
  type="email"
  placeholder="name@example.com"
/>
```

#### SearchInput

```typescript
import { SearchInput } from "@jem-open/jem-ui"
```

**Props:** extends Input props

| Prop | Type | Default | Description |
|---|---|---|---|
| `onClear` | `() => void` | — | Called when clear (X) button is clicked |

Shows a search icon on the left and a clear (X) button when there is a value.

**Example:**

```tsx
const [query, setQuery] = useState("")

<SearchInput
  value={query}
  onChange={(e) => setQuery(e.target.value)}
  onClear={() => setQuery("")}
  placeholder="Search..."
/>
```

#### Checkbox

```typescript
import { Checkbox } from "@jem-open/jem-ui"
```

**Props:** extends `React.ComponentProps<typeof CheckboxPrimitive.Root>` (Radix)

Standard Radix checkbox props: `checked`, `onCheckedChange`, `disabled`, `name`, `value`.

**Example:**

```tsx
<Checkbox checked={agreed} onCheckedChange={setAgreed} />
```

#### CheckboxWithLabel

```typescript
import { CheckboxWithLabel } from "@jem-open/jem-ui"
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `label` | `string` | required | Label text |
| `description` | `string` | — | Description below label |

Plus all Checkbox props.

**Example:**

```tsx
<CheckboxWithLabel
  label="Accept terms"
  description="You agree to our terms of service"
  checked={agreed}
  onCheckedChange={setAgreed}
/>
```

#### CheckboxCard

```typescript
import { CheckboxCard } from "@jem-open/jem-ui"
```

Same props as CheckboxWithLabel but renders as a card-style container.

**Example:**

```tsx
<CheckboxCard
  label="Email notifications"
  description="Receive email updates about your account"
  checked={emailNotifs}
  onCheckedChange={setEmailNotifs}
/>
```

#### RadioGroup / RadioGroupItem

```typescript
import { RadioGroup, RadioGroupItem } from "@jem-open/jem-ui"
```

**RadioGroup props:** extends `React.ComponentProps<typeof RadioGroupPrimitive.Root>` (Radix)
- `value`, `onValueChange`, `disabled`, `name`
- Layout: grid with gap-3

**RadioGroupItem props:** extends Radix RadioGroup.Item
- `value` (required)

**Example:**

```tsx
<RadioGroup value={plan} onValueChange={setPlan}>
  <RadioGroupItem value="free" />
  <RadioGroupItem value="pro" />
  <RadioGroupItem value="enterprise" />
</RadioGroup>
```

Also available: `RadioGroupItemWithLabel` (adds `label` and `description` props) and `RadioGroupCard` (card-style).

**RadioGroupItemWithLabel example:**

```tsx
<RadioGroup value={plan} onValueChange={setPlan}>
  <RadioGroupItemWithLabel value="free" label="Free" description="Basic features" />
  <RadioGroupItemWithLabel value="pro" label="Pro" description="All features" />
</RadioGroup>
```

#### Select (compound)

```typescript
import {
  Select, SelectTrigger, SelectContent, SelectItem, SelectValue
} from "@jem-open/jem-ui"
```

Composable compound component. Must be assembled from parts:

**Example:**

```tsx
<Select value={role} onValueChange={setRole}>
  <SelectTrigger>
    <SelectValue placeholder="Select role" />
  </SelectTrigger>
  <SelectContent>
    <SelectItem value="admin">Admin</SelectItem>
    <SelectItem value="editor">Editor</SelectItem>
    <SelectItem value="viewer">Viewer</SelectItem>
  </SelectContent>
</Select>
```

Also available: `SelectField` wraps Select with `label` and `description`:

```tsx
<SelectField label="Role" description="User's permission level">
  <Select value={role} onValueChange={setRole}>
    {/* ... same inner content */}
  </Select>
</SelectField>
```

Additional parts: `SelectGroup`, `SelectLabel`, `SelectSeparator`, `SelectScrollUpButton`, `SelectScrollDownButton`.

#### DatePicker

```typescript
import { DatePicker } from "@jem-open/jem-ui"
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `date` | `Date` | — | Selected date |
| `onDateChange` | `(date: Date \| undefined) => void` | — | Date change handler |
| `placeholder` | `string` | — | Placeholder text |
| `disabled` | `boolean` | `false` | Disabled state |
| `label` | `string` | — | Label above picker |

**Example:**

```tsx
<DatePicker
  label="Start date"
  date={startDate}
  onDateChange={setStartDate}
  placeholder="Pick a date"
/>
```

#### DateRangePicker

```typescript
import { DateRangePicker } from "@jem-open/jem-ui"
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `dateRange` | `DateRange` | — | Selected range `{ from?: Date, to?: Date }` |
| `onDateRangeChange` | `(range: DateRange \| undefined) => void` | — | Range change handler |
| `placeholder` | `string` | — | Placeholder text |
| `disabled` | `boolean` | `false` | Disabled state |
| `label` | `string` | — | Label above picker |

Shows 2 months side by side. Format: "LLL dd, y" (e.g. "Mar 09, 2026").

**Example:**

```tsx
<DateRangePicker
  label="Date range"
  dateRange={range}
  onDateRangeChange={setRange}
  placeholder="Select range"
/>
```

#### Calendar

```typescript
import { Calendar } from "@jem-open/jem-ui"
```

Standalone calendar component. Props extend React DayPicker. Usually used inside DatePicker — use DatePicker instead unless you need a custom calendar layout.

**Example:**

```tsx
<Calendar mode="single" selected={date} onSelect={setDate} />
```

#### Textarea / TextareaField

```typescript
import { Textarea } from "@jem-open/jem-ui"
```

**Textarea props:** extends `React.ComponentProps<"textarea">`. Min height: 115px. Resize disabled.

**TextareaField** adds form wrapper props:

| Prop | Type | Default | Description |
|---|---|---|---|
| `label` | `string` | — | Label text |
| `description` | `string` | — | Description below label |
| `helperText` | `string` | — | Helper text below textarea |
| `error` | `boolean` | `false` | Error state |

**Example:**

```tsx
<TextareaField
  label="Notes"
  helperText="Max 500 characters"
  placeholder="Enter your notes..."
/>
```

#### Switch / SwitchWithLabel

```typescript
import { Switch } from "@jem-open/jem-ui"
```

**Switch props:** extends Radix Switch. Key props: `checked`, `onCheckedChange`, `disabled`.

Checked: pink-900 background. Unchecked: slate-200.

**SwitchWithLabel** adds `label` and `description` props:

```tsx
<SwitchWithLabel
  label="Dark mode"
  description="Use dark theme across the app"
  checked={darkMode}
  onCheckedChange={setDarkMode}
/>
```

#### Label

```typescript
import { Label } from "@jem-open/jem-ui"
```

Radix-based label that properly associates with form inputs via `htmlFor`.

**Example:**

```tsx
<Label htmlFor="email">Email address</Label>
<Input id="email" type="email" />
```

Note: `InputField`, `TextareaField`, `SelectField`, `CheckboxWithLabel`, and `SwitchWithLabel` handle labeling automatically. Use `Label` only when building custom form layouts.

#### Upload

```typescript
import { Upload } from "@jem-open/jem-ui"
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `state` | `"default" \| "uploading" \| "uploaded"` | `"default"` | Current upload state |
| `progress` | `number` | — | Upload progress (0-100), shown during "uploading" |
| `fileName` | `string` | — | Name of uploaded file |
| `title` | `string` | — | Title text in upload area |
| `description` | `string` | — | Description text in upload area |
| `maxSize` | `string` | — | Max file size text (e.g. "10MB") |
| `onSelectFile` | `() => void` | — | Called when user clicks to select file |
| `onRemoveFile` | `() => void` | — | Called when user removes uploaded file |
| `onSubmit` | `() => void` | — | Called when user submits uploaded file |

**Example:**

```tsx
<Upload
  state={uploadState}
  progress={uploadProgress}
  fileName={file?.name}
  title="Upload document"
  description="PDF, DOC, or DOCX"
  maxSize="10MB"
  onSelectFile={handleSelectFile}
  onRemoveFile={handleRemoveFile}
  onSubmit={handleSubmit}
/>
```
