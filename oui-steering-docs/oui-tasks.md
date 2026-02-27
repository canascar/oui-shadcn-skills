# Implementation Plan: OUI-SCN-DS (OUI Theme)

## Overview

Implement the OpenSearch-branded shadcn/Tailwind CSS v4 design system. The pipeline converts Figma hex tokens to HSL CSS custom properties, generates a `globals.css` theme file with `:root` and `.dark` selectors, integrates with Tailwind v4 via `@theme`, and sets up Inter/Inter Mono fonts. Includes build-time validation for token completeness and WCAG contrast compliance.

## Tasks

- [ ] 1. Create token definitions and hex-to-HSL conversion
  - [ ] 1.1 Create the token definition file with all OUI design tokens
    - Create `src/theme/tokens.ts` defining the `DesignTokenSet` with all color tokens (light and dark hex values), radius tokens, shadow tokens, font definitions, and spacing values
    - Include all shadcn_Required_Tokens: background, foreground, primary, primary-foreground, secondary, secondary-foreground, muted, muted-foreground, accent, accent-foreground, border, input, ring, card, card-foreground, destructive, destructive-foreground, popover, popover-foreground
    - Define brand tokens: `--primary` as `#0369a1` (light) / `#0284c7` (dark), `--background` light as `#ffffff`, dark as `#0a0a0a`, `--accent` as `#e0f2fe` (light) / `#0c4a6e` (dark)
    - Dark mode uses neutral-based palette: background `#0a0a0a`, card `#171717`, secondary/muted `#262626`
    - Dark mode borders use alpha-based white: `--border` as `#ffffff33`, `--input` as `#ffffff26`
    - Dark mode `--primary-foreground` inverts to `#171717`
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8, 5.9, 5.10, 5.11, 5.12_

  - [ ] 1.2 Implement the `hexToHSL()` function
    - Create `src/theme/hex-to-hsl.ts` implementing the hex-to-HSL conversion
    - Accept hex strings with or without `#` prefix
    - Return bare HSL string in format `H S% L%` (no `hsl()` wrapper)
    - Throw descriptive error for invalid hex inputs not matching `/^#?[0-9a-fA-F]{6}$/`
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5_

  - [ ]* 1.3 Write property tests for `hexToHSL()`
    - **Property 1: HSL Format Validity** — For any valid 6-digit hex string, output matches `H S% L%` with H ∈ [0, 360), S ∈ [0, 100], L ∈ [0, 100]
    - **Validates: Requirements 1.1, 1.3**

  - [ ]* 1.4 Write property test for hex prefix equivalence
    - **Property 2: Hex Prefix Equivalence** — `hexToHSL("#" + hex)` equals `hexToHSL(hex)` for any valid hex
    - **Validates: Requirement 1.2**

  - [ ]* 1.5 Write property test for round-trip conversion
    - **Property 3: Hex-to-HSL Round Trip** — Converting hex→HSL→hex produces ΔE < 1 from original
    - **Validates: Requirement 1.5**

  - [ ]* 1.6 Write property test for invalid hex rejection
    - **Property 4: Invalid Hex Rejection** — Any string not matching `/^#?[0-9a-fA-F]{6}$/` throws a descriptive error
    - **Validates: Requirement 1.4**

- [ ] 2. Implement CSS variable generation and validation
  - [ ] 2.1 Implement the `generateCSSVariables()` function
    - Create `src/theme/generate-css.ts` implementing CSS variable generation
    - Accept a `DesignTokenSet` and mode (`"light"` | `"dark"`)
    - Return `:root { ... }` block for light mode, `.dark { ... }` block for dark mode
    - Produce exactly one `--{name}: {value}` declaration per token
    - Output must be valid, browser-parseable CSS
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [ ] 2.2 Implement token completeness validation
    - Add validation logic that checks all shadcn_Required_Tokens are present in the DesignTokenSet
    - Validate light/dark token parity (every `:root` token has a `.dark` counterpart and vice versa)
    - Halt with actionable error listing missing token names if validation fails
    - _Requirements: 2.5, 8.1, 8.2, 8.3, 8.4_

  - [ ]* 2.3 Write property test for CSS selector wrapping
    - **Property 5: CSS Selector Wrapping by Mode** — Light mode output wrapped in `:root { }`, dark mode in `.dark { }`
    - **Validates: Requirements 2.1, 2.2**

  - [ ]* 2.4 Write property test for one declaration per token
    - **Property 6: One Declaration Per Token** — Output contains exactly one `--{name}: {value}` per input token
    - **Validates: Requirement 2.3**

  - [ ]* 2.5 Write property test for valid CSS output
    - **Property 7: Valid CSS Output** — Output is syntactically valid CSS parseable by PostCSS
    - **Validates: Requirement 2.4**

  - [ ]* 2.6 Write property tests for token validation
    - **Property 11: Light/Dark Token Parity** — Every token in `:root` exists in `.dark` and vice versa
    - **Property 12: Missing Token Validation** — Missing shadcn_Required_Tokens halts pipeline with error listing missing names
    - **Validates: Requirements 5.4, 8.1, 8.2, 8.3, 8.4**

- [ ] 3. Checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 4. Implement Tailwind theme integration
  - [ ] 4.1 Implement the `buildTailwindTheme()` function
    - Create `src/theme/tailwind-theme.ts` implementing the Tailwind theme extension builder
    - Map each color token to `hsl(var(--{name}))` format
    - Include all shadcn_Required_Tokens as color keys
    - Include border-radius entries using rem values
    - Include font-family entries with system fallback stacks
    - _Requirements: 3.1, 3.2, 3.3, 3.4_

  - [ ]* 4.2 Write property test for Tailwind color mapping format
    - **Property 8: Tailwind Color Mapping Format** — Each color entry matches `hsl(var(--{name}))` format
    - **Validates: Requirement 3.1**

  - [ ]* 4.3 Write property test for Tailwind required keys
    - **Property 9: Tailwind Required Keys Completeness** — All shadcn_Required_Tokens present as color keys
    - **Validates: Requirement 3.2**

  - [ ]* 4.4 Write property test for Tailwind radius format
    - **Property 10: Tailwind Radius Rem Format** — Border-radius entries use rem values
    - **Validates: Requirement 3.3**

- [ ] 5. Build the theme CSS file and Tailwind v4 integration
  - [ ] 5.1 Implement the `buildThemeCSS()` pipeline function
    - Create `src/theme/build.ts` composing `hexToHSL`, `generateCSSVariables`, and non-color token generation
    - Output the complete `@layer base { :root { ... } .dark { ... } }` CSS string
    - Include color, radius, shadow, and font custom properties
    - Dark mode shadows minimized or set to `none` per design spec
    - _Requirements: 4.1–4.6, 5.1–5.4_

  - [ ] 5.2 Generate the `globals.css` theme file
    - Create `src/app/globals.css` (or equivalent path) with the full theme output
    - Include the `@theme` directive block for Tailwind v4 registering all color, font, and radius tokens
    - Ensure `@theme` color entries use `hsl(var(--{name}))` format
    - _Requirements: 3.5, 4.4, 5.4_

- [ ] 6. Font setup
  - [ ] 6.1 Set up Inter and Inter Mono fonts
    - Install the `@fontsource/inter` font package
    - Configure font loading with weights 300, 400, 500, 600, 700 for Inter
    - Load Inter Mono for monospace usage
    - Set `--font-sans` and `--font-mono` CSS variables in the theme
    - Use `font-display: swap` to prevent Flash of Invisible Text
    - Ensure system font fallback (`system-ui, sans-serif`) activates if font files fail to load
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6_

- [ ] 7. Dark mode toggle implementation
  - [ ] 7.1 Implement the dark mode toggle component
    - Create a `ThemeToggle` component that adds/removes `.dark` class on `document.documentElement`
    - Use `next-themes` (or equivalent) for theme state management
    - Ensure toggle updates styles instantly via CSS variable resolution without JS re-render
    - _Requirements: 6.1, 6.2, 6.3_

  - [ ]* 7.2 Write property test for dark mode toggle round trip
    - **Property 13: Dark Mode Toggle Round Trip** — Toggling dark on then off restores all computed styles to original light-mode values
    - **Validates: Requirement 6.4**

- [ ] 8. Checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 9. WCAG contrast compliance and linting
  - [ ] 9.1 Implement WCAG contrast ratio validation
    - Create `src/theme/contrast-check.ts` with a function that computes contrast ratios for all foreground/background token pairs
    - Validate 4.5:1 minimum for normal text, 3:1 minimum for large text and UI components
    - Run validation for both light and dark mode token sets
    - _Requirements: 10.1, 10.2, 10.3_

  - [ ]* 9.2 Write property test for WCAG contrast compliance
    - **Property 14: WCAG AA Contrast Compliance** — All foreground/background pairs meet 4.5:1 (normal text) and 3:1 (large text/UI) in both modes
    - **Validates: Requirements 10.1, 10.2, 10.3**

  - [ ] 9.3 Add lint rule or build check for raw color values
    - Create a utility or lint configuration that flags raw hex, rgb, or hsl color literals in component files
    - Ensure components only reference CSS custom properties via `var(--token)` or Tailwind utility classes
    - _Requirements: 9.1, 9.2, 9.3_

- [ ] 10. Final checkpoint - Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests use `fast-check` library as specified in the design
- All code is TypeScript as established in the design document
- Next.js config must set `devIndicators: false`
- The `cn()` utility (clsx + tailwind-merge) should be used for conditional class composition in components
