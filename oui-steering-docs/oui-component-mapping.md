# OUI-to-shadcn Component Mapping

## Purpose

This document ensures that shadcn/ui components in the OUI-SCN-DS project visually and behaviorally match the [OpenSearch UI (OUI) design system](https://oui.opensearch.org/1.23/#/). When implementing or customizing a shadcn component, always reference the corresponding OUI component page to align styling, spacing, states, and accessibility patterns.

OUI is the canonical design reference. shadcn/ui is the implementation framework. Where OUI defines a pattern, shadcn must follow it.

## Reference

- OUI docs: https://oui.opensearch.org
- OUI GitHub: https://github.com/opensearch-project/oui
- OUI Figma: https://www.figma.com/community/file/1319043629276905995
- OUI guidelines — Colors: https://oui.opensearch.org/1.23/#/guidelines/colors
- OUI guidelines — Accessibility: https://oui.opensearch.org/1.23/#/guidelines/accessibility

## Component Mapping

The table below maps OUI components to their shadcn/ui equivalents. When customizing a shadcn component, open the OUI reference link and match its visual appearance, interaction states, and spacing.

### Layout

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Accordion | [OUI](https://oui.opensearch.org/1.23/#/layout/accordion) | `accordion` | Match arrow placement and padding |
| Flex | [OUI](https://oui.opensearch.org/1.23/#/layout/flex) | Tailwind flex utilities | Use OUI gap/spacing tokens |
| Flyout | [OUI](https://oui.opensearch.org/1.23/#/layout/flyout) | `sheet` | Match slide direction and overlay behavior |
| Header | [OUI](https://oui.opensearch.org/1.23/#/layout/header) | Custom layout | See sidebar-component.md for header spec |
| Modal | [OUI](https://oui.opensearch.org/1.23/#/layout/modal) | `dialog` | Match overlay opacity, padding, close button |
| Page | [OUI](https://oui.opensearch.org/1.23/#/layout/page) | Custom layout | Follow OUI page structure with sidebar |
| Panel | [OUI](https://oui.opensearch.org/1.23/#/layout/panel) | `card` | Match border, shadow, and padding |
| Popover | [OUI](https://oui.opensearch.org/1.23/#/layout/popover) | `popover` | Match arrow, offset, and animation |
| Spacer | [OUI](https://oui.opensearch.org/1.23/#/layout/spacer) | Tailwind spacing utilities | Use design system spacing tokens |

### Navigation

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Breadcrumbs | [OUI](https://oui.opensearch.org/1.23/#/navigation/breadcrumbs) | `breadcrumb` | Match separator style and truncation |
| Button | [OUI](https://oui.opensearch.org/1.23/#/navigation/button) | `button` | Match all variants: primary, danger, ghost, link |
| Split Button | [OUI](https://oui.opensearch.org/1.23/#/navigation/split-button) | `button` + `dropdown-menu` | Compose from shadcn primitives |
| Collapsible nav | [OUI](https://oui.opensearch.org/1.23/#/navigation/collapsible-nav) | `collapsible` + sidebar | See sidebar-component.md |
| Context menu | [OUI](https://oui.opensearch.org/1.23/#/navigation/context-menu) | `context-menu` | Match item padding and hover states |
| Link | [OUI](https://oui.opensearch.org/1.23/#/navigation/link) | Native `<a>` with `text-primary` | Match underline and hover behavior |
| Pagination | [OUI](https://oui.opensearch.org/1.23/#/navigation/pagination) | `pagination` | Match active/disabled states |
| Side nav | [OUI](https://oui.opensearch.org/1.23/#/navigation/side-nav) | Custom sidebar | See sidebar-component.md |
| Steps | [OUI](https://oui.opensearch.org/1.23/#/navigation/steps) | `stepper` (custom) | Build from OUI step pattern |
| Tabs | [OUI](https://oui.opensearch.org/1.23/#/navigation/tabs) | `tabs` | Match underline style and active indicator |

### Display

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Avatar | [OUI](https://oui.opensearch.org/1.23/#/display/avatar) | `avatar` | Always use `rounded-full` per design system |
| Badge | [OUI](https://oui.opensearch.org/1.23/#/display/badge) | `badge` | Match color variants and sizing |
| Callout | [OUI](https://oui.opensearch.org/1.23/#/display/callout) | `alert` | Match icon placement and color per severity |
| Card | [OUI](https://oui.opensearch.org/1.23/#/display/card) | `card` | Match padding, shadow, and border radius |
| Description list | [OUI](https://oui.opensearch.org/1.23/#/display/description-list) | Custom with Tailwind | Follow OUI term/description layout |
| Empty prompt | [OUI](https://oui.opensearch.org/1.23/#/display/empty-prompt) | Custom composition | Use card + centered content pattern |
| Icons | [OUI](https://oui.opensearch.org/1.23/#/display/icons) | `lucide-react` | Use lucide equivalents; match OUI icon sizes |
| Loading | [OUI](https://oui.opensearch.org/1.23/#/display/loading) | `skeleton` | Match shimmer animation and sizing |
| Progress | [OUI](https://oui.opensearch.org/1.23/#/display/progress) | `progress` | Match bar height and color |
| Stat | [OUI](https://oui.opensearch.org/1.23/#/display/stat) | Custom composition | Use card + large text pattern from OUI |
| Text | [OUI](https://oui.opensearch.org/1.23/#/display/text) | Typography utilities | Follow design system type scale |
| Title | [OUI](https://oui.opensearch.org/1.23/#/display/title) | Heading elements | Follow design system font weights |
| Toast | [OUI](https://oui.opensearch.org/1.23/#/display/toast) | `sonner` or `toast` | Match position, duration, and color |
| Tooltip | [OUI](https://oui.opensearch.org/1.23/#/display/tooltip) | `tooltip` | Match delay, offset, and arrow |
| Tour | [OUI](https://oui.opensearch.org/1.23/#/display/tour) | Custom composition | Build from popover + step pattern |

### Forms

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Form controls | [OUI](https://oui.opensearch.org/1.23/#/forms/form-controls) | `input`, `textarea`, `checkbox`, `radio-group`, `switch`, `select` | Match sizing, border, and focus ring |
| Form layouts | [OUI](https://oui.opensearch.org/1.23/#/forms/form-layouts) | `form` with layout utilities | Match label placement and spacing |
| Form validation | [OUI](https://oui.opensearch.org/1.23/#/forms/form-validation) | `form` (react-hook-form) | Match error message style and placement |
| Combo box | [OUI](https://oui.opensearch.org/1.23/#/forms/combo-box) | `combobox` | Match dropdown behavior and filtering |
| Color selection | [OUI](https://oui.opensearch.org/1.23/#/forms/color-selection) | Custom | Build from OUI color picker pattern |
| Date picker | [OUI](https://oui.opensearch.org/1.23/#/forms/date-picker) | `date-picker` / `calendar` | Match calendar layout and range selection |
| Filter group | [OUI](https://oui.opensearch.org/1.23/#/forms/filter-group) | `toggle-group` | Match pill/chip styling |
| Range sliders | [OUI](https://oui.opensearch.org/1.23/#/forms/range-sliders) | `slider` | Match track and thumb styling |
| Search bar | [OUI](https://oui.opensearch.org/1.23/#/forms/search-bar) | `input` with icon | Match search icon placement and clear button |
| Selectable | [OUI](https://oui.opensearch.org/1.23/#/forms/selectable) | `command` | Match list item styling and selection |

### Tabular Content

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Data grid | [OUI](https://oui.opensearch.org/1.23/#/tabular-content/data-grid) | `data-table` (tanstack) | Match header, row, and cell styling |
| Tables | [OUI](https://oui.opensearch.org/1.23/#/tabular-content/tables) | `table` | Match striping, hover, and border patterns |

### Editors & Syntax

| OUI Component | OUI Reference | shadcn Equivalent | Notes |
|---|---|---|---|
| Code | [OUI](https://oui.opensearch.org/1.23/#/editors-syntax/code) | `<code>` with `font-mono` | Use `--font-mono` token |
| Markdown editor | [OUI](https://oui.opensearch.org/1.23/#/editors-syntax/markdown-editor) | Custom or third-party | Match toolbar and preview styling |

## Rules

1. Before building or customizing any shadcn component, open the corresponding OUI reference link and study its visual design, spacing, and interaction states.
2. All color, spacing, radius, and shadow values must come from the OUI design system tokens defined in `oui-design-system.md` — never use raw values.
3. When OUI defines a component variant (e.g., button danger, callout warning), the shadcn equivalent must support the same variant with matching visual treatment.
4. Accessibility patterns from OUI (aria attributes, keyboard navigation, focus management) must be preserved in the shadcn implementation.
5. If a shadcn component has no OUI equivalent, it may be used as-is but must still follow the design system tokens.
6. If an OUI component has no shadcn equivalent, build it as a custom component following OUI's visual spec and the design system tokens.
