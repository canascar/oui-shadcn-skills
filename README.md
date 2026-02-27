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
