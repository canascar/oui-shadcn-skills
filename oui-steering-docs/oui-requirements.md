# Requirements Document

## Introduction

This document defines the requirements for the OpenSearch-branded OUI-SCN-DS design system (OUI Theme). The system converts Figma design tokens into CSS custom properties consumed by shadcn/ui components via Tailwind CSS v4, supporting both light and dark modes. Requirements are derived from the technical design covering the token-to-CSS pipeline, theme file generation, Tailwind integration, font setup, and dark mode toggling.

## Glossary

- **Token_Pipeline**: The build-time process that converts Figma hex color tokens into HSL-formatted CSS custom properties and outputs a theme CSS file.
- **Theme_CSS_File**: The `globals.css` file containing all CSS custom property declarations under `:root` (light) and `.dark` (dark) selectors.
- **hexToHSL**: A pure function that converts a 6-digit hex color string to a bare HSL string in the format `H S% L%`.
- **generateCSSVariables**: A function that takes a DesignTokenSet and a mode ("light" or "dark") and returns a CSS block string with custom property declarations.
- **buildTailwindTheme**: A function that takes a DesignTokenSet and returns a Tailwind theme extension object mapping CSS custom properties to utility classes.
- **DesignTokenSet**: A structured object containing all design tokens: colors, radii, shadows, fonts, and spacing.
- **HSLString**: A string in the format `H S% L%` where H ∈ [0, 360), S ∈ [0, 100], L ∈ [0, 100], without an `hsl()` wrapper.
- **shadcn_Required_Tokens**: The set of semantic color tokens required by shadcn/ui: background, foreground, primary, primary-foreground, secondary, secondary-foreground, muted, muted-foreground, accent, accent-foreground, border, input, ring, card, card-foreground, destructive, destructive-foreground, popover, popover-foreground.
- **Tailwind_Theme_Extension**: A configuration object that maps CSS custom properties to Tailwind utility classes for colors, border-radius, font-family, and box-shadow.
- **Dark_Mode_Toggle**: The mechanism that adds or removes the `.dark` class on the document root element to switch between light and dark themes.

## Requirements

### Requirement 1: Hex-to-HSL Color Conversion

**User Story:** As a design system maintainer, I want Figma hex tokens automatically converted to HSL format, so that shadcn/ui components can consume them as CSS custom properties.

#### Acceptance Criteria

1. WHEN a valid 6-digit hex color string is provided, THE hexToHSL function SHALL return an HSLString in the format `H S% L%` where H ∈ [0, 360), S ∈ [0, 100], L ∈ [0, 100]
2. WHEN a hex string includes a `#` prefix, THE hexToHSL function SHALL strip the prefix before conversion and produce the same result as without the prefix
3. THE hexToHSL function SHALL return bare HSL values without an `hsl()` wrapper
4. IF a malformed hex value is provided (not matching `/^#?[0-9a-fA-F]{6}$/`), THEN THE hexToHSL function SHALL throw a descriptive error identifying the invalid value
5. WHEN converting a hex color to HSL and back, THE round-trip result SHALL be perceptually equivalent to the original (ΔE < 1)

### Requirement 2: CSS Variable Generation

**User Story:** As a design system maintainer, I want CSS custom property blocks generated from design tokens, so that the theme file contains all token definitions for both light and dark modes.

#### Acceptance Criteria

1. WHEN a DesignTokenSet and mode "light" are provided, THE generateCSSVariables function SHALL return a `:root { ... }` CSS block containing all token declarations
2. WHEN a DesignTokenSet and mode "dark" are provided, THE generateCSSVariables function SHALL return a `.dark { ... }` CSS block containing all token declarations
3. THE generateCSSVariables function SHALL produce exactly one `--{name}: {value}` declaration per token in the input set
4. THE generateCSSVariables function SHALL produce output that is valid, browser-parseable CSS
5. IF the DesignTokenSet is missing any shadcn_Required_Tokens, THEN THE generateCSSVariables function SHALL report the missing token names before producing output

### Requirement 3: Tailwind Theme Integration

**User Story:** As a frontend developer, I want design tokens available as Tailwind utility classes, so that I can style components using `bg-primary`, `text-muted-foreground`, and similar utilities.

#### Acceptance Criteria

1. THE buildTailwindTheme function SHALL map each color token to `hsl(var(--{name}))` format
2. THE buildTailwindTheme function SHALL include all shadcn_Required_Tokens as color keys in the output
3. THE buildTailwindTheme function SHALL include border-radius entries using rem values
4. THE buildTailwindTheme function SHALL include font-family entries with system fallback stacks
5. WHEN the Tailwind v4 `@theme` directive is used, THE Theme_CSS_File SHALL register all color, font, and radius tokens under the `@theme` block

### Requirement 4: Light Mode Token Definitions

**User Story:** As a frontend developer, I want all OUI brand tokens defined for light mode, so that components render with the correct OpenSearch visual identity by default.

#### Acceptance Criteria

1. THE Theme_CSS_File SHALL define `--primary` as the HSL equivalent of `#0369a1` (sky-700) in the `:root` selector
2. THE Theme_CSS_File SHALL define `--background` as the HSL equivalent of `#ffffff` in the `:root` selector
3. THE Theme_CSS_File SHALL define `--accent` as the HSL equivalent of `#e0f2fe` (sky-100) in the `:root` selector
4. THE Theme_CSS_File SHALL define all shadcn_Required_Tokens in the `:root` selector
5. THE Theme_CSS_File SHALL define radius tokens (`--radius`, `--radius-md`, `--radius-lg`, `--radius-xl`, `--radius-2xl`) in the `:root` selector
6. THE Theme_CSS_File SHALL define shadow tokens (`--shadow-xs`, `--shadow-sm`, `--shadow-lg`) in the `:root` selector

### Requirement 5: Dark Mode Token Definitions

**User Story:** As a frontend developer, I want all OUI brand tokens defined for dark mode, so that components render correctly when the user switches to dark theme.

#### Acceptance Criteria

1. THE Theme_CSS_File SHALL define `--background` as the HSL equivalent of `#0a0a0a` (neutral-950) in the `.dark` selector
2. THE Theme_CSS_File SHALL define `--foreground` as a light neutral color (HSL equivalent of `#fafafa`) in the `.dark` selector
3. WHILE dark mode is active, THE Theme_CSS_File SHALL minimize or disable shadow tokens to reflect that dark surfaces do not cast visible shadows
4. THE Theme_CSS_File SHALL define every token present in `:root` with a corresponding entry in the `.dark` selector
5. THE Theme_CSS_File SHALL define `--primary` as the HSL equivalent of `#0284c7` (sky-600) in the `.dark` selector
6. THE Theme_CSS_File SHALL define `--primary-foreground` as the HSL equivalent of `#171717` (neutral-900) in the `.dark` selector
7. THE Theme_CSS_File SHALL define `--card` as the HSL equivalent of `#171717` (neutral-900) in the `.dark` selector
8. THE Theme_CSS_File SHALL define `--secondary` and `--muted` as the HSL equivalent of `#262626` (neutral-800) in the `.dark` selector
9. THE Theme_CSS_File SHALL define `--accent` as the HSL equivalent of `#0c4a6e` (sky-900) in the `.dark` selector
10. THE Theme_CSS_File SHALL define `--border` as alpha-based white (`#ffffff33` / 20% opacity) in the `.dark` selector
11. THE Theme_CSS_File SHALL define `--input` as alpha-based white (`#ffffff26` / 15% opacity) in the `.dark` selector
12. THE Theme_CSS_File SHALL define `--muted-foreground` as the HSL equivalent of `#a3a3a3` (neutral-400) in the `.dark` selector

### Requirement 6: Dark Mode Toggle

**User Story:** As an end user, I want to toggle between light and dark themes, so that I can use the interface in my preferred visual mode.

#### Acceptance Criteria

1. WHEN the Dark_Mode_Toggle is activated, THE system SHALL add the `.dark` class to the document root element
2. WHEN the Dark_Mode_Toggle is deactivated, THE system SHALL remove the `.dark` class from the document root element
3. WHEN the `.dark` class is toggled, THE system SHALL update all component styles instantly via CSS variable resolution without a JavaScript re-render
4. WHEN dark mode is toggled on and then off, THE system SHALL restore all computed styles to their original light-mode values

### Requirement 7: Font Setup

**User Story:** As a frontend developer, I want Inter and Inter Mono fonts loaded and registered, so that all UI text renders with the correct OpenSearch typography.

#### Acceptance Criteria

1. THE Font_Setup SHALL load the Inter font with weights 300 (Light), 400 (Normal), 500 (Medium), 600 (Semibold), and 700 (Bold)
2. THE Font_Setup SHALL load the Inter Mono font for monospace usage
3. THE Theme_CSS_File SHALL define `--font-sans` as `"Inter", system-ui, sans-serif`
4. THE Theme_CSS_File SHALL define `--font-mono` as `"Inter Mono", ui-monospace, monospace`
5. THE Font_Setup SHALL use `font-display: swap` to prevent Flash of Invisible Text
6. IF the Inter font files fail to load, THEN THE system SHALL fall back to the system font stack (`system-ui, sans-serif`) without visible breakage

### Requirement 8: Token Completeness Validation

**User Story:** As a design system maintainer, I want build-time validation of token completeness, so that missing or mismatched tokens are caught before deployment.

#### Acceptance Criteria

1. WHEN the Token_Pipeline runs, THE system SHALL validate that all shadcn_Required_Tokens are present in the DesignTokenSet
2. IF a token is defined in `:root` but missing from `.dark`, THEN THE Token_Pipeline SHALL report the mismatched token name
3. IF a token is defined in `.dark` but missing from `:root`, THEN THE Token_Pipeline SHALL report the mismatched token name
4. IF any required token is missing from the DesignTokenSet, THEN THE Token_Pipeline SHALL halt with an actionable error message listing the missing tokens

### Requirement 9: No Raw Color Values in Components

**User Story:** As a design system maintainer, I want to enforce that components only use token references, so that theme changes propagate consistently across the entire UI.

#### Acceptance Criteria

1. THE system SHALL ensure all component color values reference CSS custom properties via `var(--token)` or Tailwind utility classes
2. THE system SHALL reject any component that uses raw hex, rgb, or hsl color literals for themed properties
3. WHEN a component needs a color value, THE component SHALL reference a design token rather than a hardcoded value

### Requirement 10: WCAG Contrast Compliance

**User Story:** As a frontend developer, I want foreground/background color pairs to meet WCAG AA contrast ratios, so that the UI is accessible to users with visual impairments.

#### Acceptance Criteria

1. THE Theme_CSS_File SHALL define foreground/background color pairs that achieve a contrast ratio of at least 4.5:1 for normal text
2. THE Theme_CSS_File SHALL define foreground/background color pairs that achieve a contrast ratio of at least 3:1 for large text and UI components
3. WHILE dark mode is active, THE Theme_CSS_File SHALL maintain the same minimum contrast ratios as light mode
