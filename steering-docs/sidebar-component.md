# OUI Sidebar Component Specification

## Overview

The OUI Sidebar is a collapsible navigation component for OpenSearch Dashboards applications. It provides branded navigation with the OpenSearch logo, a list of nav links, and a user footer. The collapse/expand control lives outside the sidebar in the page header.

Developers can customize the navigation links but must preserve the branding, layout structure, and interaction patterns described here.

## Architecture

The sidebar system consists of three pieces:

1. `Sidebar` component — the sidebar itself (receives `collapsed` as a prop)
2. `OpenSearchLogo` component — the branded SVG logomark
3. Parent layout — owns the `collapsed` state and renders the toggle button in the header

```
┌──────────────────────────────────────────────────┐
│ Layout (owns collapsed state)                    │
│ ┌────────────┐ ┌──────────────────────────────┐  │
│ │  Sidebar   │ │  Header                      │  │
│ │  (props:   │ │  [Toggle] Page Title  [Theme]│  │
│ │  collapsed)│ ├──────────────────────────────┤  │
│ │            │ │  Main content                │  │
│ │            │ │                              │  │
│ └────────────┘ └──────────────────────────────┘  │
└──────────────────────────────────────────────────┘
```

## Sidebar Component

### Props

| Prop | Type | Required | Description |
|---|---|---|---|
| `collapsed` | `boolean` | Yes | Controls whether the sidebar is in collapsed (icon-only) or expanded state |

### Sections

The sidebar has three vertical sections, top to bottom:

#### 1. Header (fixed height: `h-14`)

- Contains the OpenSearch logo (`size="default"`, 24px) linking to `/` (root)
- When expanded: logo + "OpenSearch" text label
- When collapsed: logo only, centered horizontally via `justify-center`
- Bottom border separates header from nav

#### 2. Navigation (flex-1, scrollable)

- Vertical list of nav links with `space-y-1` gap
- Each link: icon (h-5 w-5) + label text
- When collapsed: icon only, with `title` attribute for tooltip
- Active state: `bg-accent text-accent-foreground font-medium`
- Inactive state: `text-muted-foreground` with `hover:bg-accent/50 hover:text-foreground`
- Each link uses `rounded-[var(--radius-lg)]` border radius
- Active route detected via `usePathname()`

#### 3. Footer (border-top)

- User avatar: 32px circle with `bg-primary text-primary-foreground`, shows initial
- When expanded: avatar + name + email
- When collapsed: avatar only, centered

### Dimensions

| State | Width |
|---|---|
| Expanded | `w-60` (240px) |
| Collapsed | `w-16` (64px) |

### Visual Properties

- Background: `bg-card`
- Right border: `border-r`
- Top-right corner radius: `rounded-tr-[var(--radius-2xl)]` (16px)
- Transition: `transition-all duration-200`
- Full viewport height: `h-screen`

## Layout Integration

The parent layout must:

1. Be a `"use client"` component (manages state)
2. Own the `collapsed` state via `useState(false)`
3. Pass `collapsed` to `<Sidebar />`
4. Render the toggle button in the header bar (not inside the sidebar)

### Header Bar

- Height: `h-14` (matches sidebar header)
- Left side: toggle button + page title
- Right side: theme toggle
- Toggle button: `variant="ghost" size="icon"` with `h-8 w-8`
- Icons: `PanelLeftClose` (when expanded) / `PanelLeftOpen` (when collapsed) from lucide-react, `h-4 w-4`

### Layout Structure

```
<div className="flex h-screen overflow-hidden">
  <Sidebar collapsed={collapsed} />
  <div className="flex flex-1 flex-col overflow-hidden">
    <header>...</header>
    <main className="flex-1 overflow-y-auto p-6">{children}</main>
  </div>
</div>
```

## OpenSearch Logo Component

Inline SVG with `viewBox="0 0 42.6667 42.6667"`. Three paths:

- Outer arc + bottom swirl: `fill-primary`
- Center swirl: `fill-foreground`

Size variants:

| Variant | Pixels | Usage |
|---|---|---|
| `sm` | 16 | Favicons, compact UI |
| `default` | 24 | Sidebar header, nav bars |
| `lg` | 32 | Page headers |
| `xl` | 48 | Login screens, hero sections |

Includes `aria-label="OpenSearch"` and `role="img"` for accessibility.

## Customization Rules

### What developers CAN change

- Navigation items: add, remove, reorder, change icons and labels
- Route paths: update `href` values to match their app structure
- Footer user info: replace with actual user data
- Page title in the header bar

### What developers MUST NOT change

- OpenSearch logo placement, size, and link target (`/`)
- Sidebar width values (`w-60` expanded, `w-16` collapsed)
- Header height (`h-14`) — must match between sidebar and layout header
- Top-right corner radius (`--radius-2xl`)
- Background token (`bg-card`)
- Collapse/expand toggle placement (in the layout header, not the sidebar)
- Active/inactive nav link styling tokens
- The three-section layout (header → nav → footer)
- Transition timing (`duration-200`)

## Token Dependencies

All styling references design system tokens — no raw color values:

| Usage | Token / Class |
|---|---|
| Sidebar background | `bg-card` |
| Text | `text-foreground`, `text-muted-foreground` |
| Active nav link | `bg-accent text-accent-foreground` |
| Hover nav link | `hover:bg-accent/50 hover:text-foreground` |
| User avatar | `bg-primary text-primary-foreground` |
| Borders | `border-r`, `border-b`, `border-t` (uses `--border` token) |
| Nav link radius | `var(--radius-lg)` |
| Sidebar corner | `var(--radius-2xl)` |
| Avatar radius | `var(--radius-lg)` |

## Dark Mode

The sidebar inherits dark mode automatically through CSS custom properties. No component-level dark mode logic is needed. The `.dark` class on `<html>` swaps all token values.

## Dependencies

| Package | Purpose |
|---|---|
| `next/link` | Client-side navigation |
| `next/navigation` | `usePathname()` for active state |
| `lucide-react` | Nav icons, toggle icons |
| `@/lib/utils` | `cn()` utility |
| `@/components/opensearch-logo` | Branded logo SVG |
| `@/components/ui/button` | Toggle button, ghost variant |
