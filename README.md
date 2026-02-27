# OUI Shadcn Skills

This repository contains the specification and planning documents for the OUI-SCN-DS project — an OpenSearch-branded shadcn/Tailwind CSS v4 design system. The system converts Figma design tokens into CSS custom properties consumed by shadcn/ui components, supporting both light and dark modes.

## Adding Steering Docs to Kiro

To use these documents as steering files in Kiro, copy them into your project's `.kiro/steering/` directory:

```bash
mkdir -p .kiro/steering
cp oui-steering-docs/*.md .kiro/steering/
```

By default, all steering files in `.kiro/steering/` are automatically included in every agent interaction. If you'd prefer a file to only load when relevant, add a front-matter block at the top of the file:

```markdown
---
inclusion: fileMatch
fileMatchPattern: 'src/theme/**'
---
```

This tells Kiro to only include that steering file when a matching file is read into context. For example, `oui-design-system.md` could be scoped to `src/theme/**` so it's only pulled in when working on theme files.

To make a file available on-demand via the `#` context key in chat instead of auto-included:

```markdown
---
inclusion: manual
---
```

You can also reference other project files from within a steering doc using `#[[file:path/to/file]]` — useful for linking an OpenAPI spec or GraphQL schema directly into the steering context.

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

## Agent Workflow: From Page to Production

When a Figma page or design mockup is sent to the agent for design and development, the following steps are taken:

### Phase 1: Design Analysis
1. The agent receives the Figma page/frame and identifies the UI components, layout structure, and visual properties present in the design.
2. The agent cross-references the design against `oui-steering-docs/oui-design-system.md` to map visual elements to existing design tokens (colors, typography, spacing, radii, shadows).
3. Any component patterns are matched to existing specs (e.g., sidebar layout maps to `oui-steering-docs/oui-sidebar-component.md`).

### Phase 2: Token Verification
4. The agent verifies that all colors, fonts, and spacing used in the design correspond to tokens defined in the design system. If a value doesn't match a known token, the agent flags it and asks for clarification.
5. Color values from the design are run through the `hexToHSL()` pipeline to confirm they resolve to the expected CSS custom properties in both light and dark modes.

### Phase 3: Component Scaffolding
6. The agent identifies which shadcn/ui primitives are needed (Button, Card, Input, etc.) and scaffolds the component file using the project's conventions (`cn()` utility, Tailwind classes, CSS variable references).
7. Layout structure is built following the patterns in the design docs — flex containers, spacing tokens, responsive breakpoints.
8. All color references use design system tokens via Tailwind utilities (`bg-primary`, `text-muted-foreground`) or `var(--token)` — never raw hex/rgb values.

### Phase 4: Styling & Theming
9. The agent applies the correct token classes for backgrounds, text, borders, shadows, and radii as defined in `oui-design-system.md`.
10. Dark mode support is verified by ensuring all styled elements use CSS custom properties that swap automatically when the `.dark` class is present.
11. Typography is set using the Inter font family with the appropriate weight and size from the type scale.

### Phase 5: Accessibility & Validation
12. The agent checks that foreground/background color pairs meet WCAG AA contrast ratios (4.5:1 for normal text, 3:1 for large text and UI components) using the contrast validation logic from `src/theme/contrast-check.ts`.
13. Interactive elements include proper ARIA attributes, keyboard navigation, and focus states.
14. The component is linted to ensure no raw color literals are present.

### Phase 6: Integration & Review
15. The component is wired into the application layout, receiving any required props (e.g., `collapsed` for the sidebar).
16. The agent runs a build check to confirm no type errors, missing imports, or token mismatches.
17. The completed component is presented for review, with notes on any design decisions or deviations from the original mockup.

### Summary Flow

```
Figma Page → Design Analysis → Token Verification → Component Scaffolding
    → Styling & Theming → Accessibility & Validation → Integration & Review
```

Each phase references the steering docs as the source of truth. The agent will pause and ask for input if the design contains elements that don't map cleanly to existing tokens or component specs.
