# OUI Shadcn Skills

This repository contains the specification and planning documents for the OUI-SCN-DS project — an OpenSearch-branded shadcn/Tailwind CSS v4 design system. The system converts Figma design tokens into CSS custom properties consumed by shadcn/ui components, supporting both light and dark modes.

## Files

All documents live in the `oui-steering-docs/` folder.

### oui-requirements.md
Formal requirements for the design system. Covers hex-to-HSL conversion, CSS variable generation, Tailwind theme integration, light/dark mode token definitions, dark mode toggling, font setup, token validation, raw color enforcement, and WCAG AA contrast compliance. Each requirement includes user stories and acceptance criteria.

### oui-design.md
Technical design document detailing the architecture and implementation approach. Includes the token-to-CSS pipeline, component interfaces (`globals.css`, Tailwind config, font setup), data models, function specifications with formal pre/postconditions, algorithmic pseudocode, correctness properties, error handling, testing strategy, and dependency list.

### oui-design-system.md
The canonical design token reference. Defines the OpenSearch logo (SVG markup, React component API, size variants, color rules), semantic color tokens, brand tokens, typography (Inter/Inter Mono, weights, type scale), spacing system (4px base), border radius, shadows, opacity, sizing, and dark mode guidelines. All UI development must conform to these tokens.

### oui-sidebar-component.md
Component specification for the collapsible OUI Sidebar. Documents the three-section layout (header, navigation, footer), expanded/collapsed states, prop interface, dimension values, visual properties, layout integration pattern, customization rules (what can and cannot be changed), token dependencies, and required packages.

### oui-tasks.md
Implementation plan broken into 10 task groups with subtasks. Covers token definitions, hex-to-HSL conversion, CSS generation, validation, Tailwind integration, theme file output, font setup, dark mode toggle, WCAG compliance, and lint rules. Includes checkpoints, requirement traceability, and notes on optional property-based tests.


## How to Implement

The implementation follows a phased approach outlined in `oui-steering-docs/oui-tasks.md`. Here's the high-level order:

### 1. Token Definitions & Hex-to-HSL Conversion
- Create `src/theme/tokens.ts` with all OUI design tokens (colors, radii, shadows, fonts, spacing)
- Implement `hexToHSL()` in `src/theme/hex-to-hsl.ts` to convert Figma hex values to bare HSL strings

### 2. CSS Variable Generation & Validation
- Implement `generateCSSVariables()` in `src/theme/generate-css.ts` to produce `:root` and `.dark` CSS blocks
- Add build-time validation ensuring all required shadcn tokens are present and light/dark parity is maintained

### 3. Tailwind Theme Integration
- Implement `buildTailwindTheme()` in `src/theme/tailwind-theme.ts` mapping tokens to `hsl(var(--name))` format for Tailwind utility classes

### 4. Theme CSS File & Tailwind v4
- Implement `buildThemeCSS()` in `src/theme/build.ts` composing the full pipeline
- Generate `src/app/globals.css` with all token declarations and the Tailwind v4 `@theme` directive

### 5. Font Setup
- Install `@fontsource/inter` and configure Inter (weights 300–700) and Inter Mono with `font-display: swap`

### 6. Dark Mode Toggle
- Create a `ThemeToggle` component using `next-themes` to swap the `.dark` class on the document root

### 7. WCAG Compliance & Linting
- Implement contrast ratio validation in `src/theme/contrast-check.ts` (4.5:1 normal text, 3:1 large text/UI)
- Add a lint rule to flag raw color values in components

### Dependencies

| Package | Purpose |
|---|---|
| `tailwindcss` v4.x | Utility-first CSS framework |
| `shadcn/ui` | Component library |
| `@fontsource/inter` | Inter + Inter Mono fonts |
| `next` v15.x | Next.js App Router |
| `next-themes` | Dark mode toggle |
| `fast-check` | Property-based testing (optional) |

See `oui-steering-docs/oui-tasks.md` for the full task breakdown with requirement traceability.
