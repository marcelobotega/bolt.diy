## ADDED Requirements

### Requirement: Jumbo system prompt steers LLM toward Nuxt 4 and Vue 3
The `getJumboPrompt` function SHALL return a system prompt string that instructs the LLM to generate Nuxt 4 applications using Vue 3 Composition API with `<script setup lang="ts">` syntax. The prompt SHALL explicitly forbid generating React, JSX, or `.tsx` files. The prompt SHALL state "The year is 2026".

#### Scenario: Framework preference is Nuxt 4
- **WHEN** the Jumbo prompt is active and the user requests a web application
- **THEN** the LLM prompt SHALL instruct generation of a Nuxt 4 project with `nuxt.config.ts`, Vue SFCs (`.vue` files), and `<script setup lang="ts">` blocks

#### Scenario: React and JSX are forbidden
- **WHEN** the Jumbo prompt is active
- **THEN** the `technology_preferences` section SHALL contain explicit negative instructions forbidding React, JSX, `.tsx`, and `.jsx` file generation

### Requirement: Jumbo prompt delivers Kompas design tokens via Tailwind CSS theme extension
The prompt SHALL instruct the LLM to create a `tailwind.config.ts` that extends Tailwind's default theme with the complete Kompas token palette from `JUMBO_THEME_REFERENCE.md`. The `@nuxtjs/tailwindcss` Nuxt module SHALL be used for integration. The prompt SHALL NOT instruct creation of a standalone `kompas-tokens.css` file.

#### Scenario: Tailwind config extends theme with Kompas colors
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL instruct the LLM to create a `tailwind.config.ts` whose `theme.extend.colors` includes: brand colors (full Banana scale from Banana 10 `#FDF8E8` through Banana 120 `#BE9212`, with Banana 100 `#EEB717` as `kompas-primary`), greys (full Oyster scale from Oyster 10 `#F1F1F1` through Oyster 180 `#171717`), semantic colors (Apple green, Carrot orange, Tomato red, Berry blue, Eggplant purple — each with their full token scale), and neutrals (Milk `#FFFFFF`, Olive `#000000`)

#### Scenario: Kompas semantic color mappings are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL include semantic mappings in the Tailwind theme for text roles (default `#171717`, secondary `#757575`, disabled `#5E5E5E`, inverted `#FFFFFF`, brand `#EEB717`, positive `#09772B`, error `#BA0000`, warning `#EC7022`, prominent `#E90000`), background roles (default `#FFFFFF`, support `#F1F1F1`, brand `#EEB717`, positive `#09772B`, error `#BA0000`, warning-subtle `#FDF1E9`, positive-subtle `#E7F9ED`, prominent-subtle `#FDE5E5`, highlight-subtle `#FDF8E8`, loading `#F5D474`, disabled `#F1F1F1`), and border roles (default `#E3E3E3`, hover `#464646`, strong `#000000`, error `#BA0000`, positive `#0B8B32`, focus `#0A4FFF`)

#### Scenario: Kompas button, input, banner, and toggle tokens are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL include in the Tailwind theme: button colors (primary bg/hover/active, primary text, secondary bg/hover/active, secondary border, positive bg, positive text, disabled bg, disabled text), input states (default border `#757575`, hover border `#464646`, error border `#BA0000`), banner colors (7 types — yellow/highlight, orange/warning, green/success, red/error, blue/info, purple/new — each with light and dark variants), and toggle switch colors (off `#757575`, on `#0B8B32`, handle `#FFFFFF`)

#### Scenario: Kompas typography tokens are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** the Tailwind theme extension SHALL include: `fontFamily` with "Jumbo TheSans" and system sans-serif fallback, `fontSize` entries for sizes 12px through 64px, `fontWeight` entries for regular (400), bold (700), and strong (900), and line height values 1.2 (heading) and 1.5 (paragraph)

#### Scenario: Kompas spacing, border radius, shadows, and breakpoints are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** the Tailwind theme extension SHALL include: `spacing` scale (4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64, 80 — all values in px), `borderRadius` values (4px, 8px, 12px, 16px, pill/100px), `boxShadow` values (small: `0 1px 2px rgba(0,0,0,0.3)`, medium: `0 5px 10px rgba(0,0,0,0.3)`), and `screens` breakpoints (xs: 375px, sm: 576px, md: 768px, lg: 992px, xl: 1200px, 2xl: 1600px)

#### Scenario: Dark mode tokens are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL include dark mode token values: background `#1A1A1A`, text `#FFFFFF`, secondary text `#C8C8C8`, borders `#000000`, support bg `#232323`, error `#FF2020`, input bg `#232323`, panel bg `#2F2F2F`

#### Scenario: Focus ring is preserved
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL specify focus ring color as `#0A4FFF` (accessibility blue) and instruct the LLM NOT to override it with brand colors

#### Scenario: Tailwind utility classes are used in components
- **WHEN** the Jumbo prompt is active
- **THEN** the prompt SHALL instruct the LLM to use Tailwind utility classes throughout components (e.g., `bg-kompas-primary`, `text-kompas-text-primary`, `rounded-kompas-md`, `shadow-kompas-sm`) rather than CSS custom properties or inline styles

#### Scenario: No standalone kompas-tokens.css file
- **WHEN** the Jumbo prompt is active
- **THEN** the prompt SHALL NOT instruct the LLM to create a `kompas-tokens.css` file. The `tailwind.config.ts` serves as the single configuration point for all Kompas styling.

### Requirement: Jumbo prompt includes Pinia state management
The prompt SHALL include instructions for using Pinia as the state management solution, replacing references to Zustand, Jotai, or other React state libraries.

#### Scenario: Pinia is the default state management
- **WHEN** the Jumbo prompt is active and the user requests state management
- **THEN** the prompt SHALL instruct the LLM to use Pinia with `defineStore` and the setup syntax

### Requirement: Jumbo prompt includes Vue Router file-based routing
The prompt SHALL instruct the LLM to use Nuxt's file-based routing convention (`pages/` directory) and `definePageMeta` for route metadata, replacing React Router references.

#### Scenario: File-based routing is the default
- **WHEN** the Jumbo prompt is active and the user requests routing
- **THEN** the prompt SHALL instruct the LLM to create routes as `.vue` files under the `pages/` directory using Nuxt's auto-routing convention

### Requirement: Jumbo prompt preserves boltArtifact protocol
The `boltArtifact` and `boltAction` protocol rules, action types (`file`, `shell`, `start`, `supabase`), and action ordering (package.json first, then install, then config, then source, then start) SHALL be copied verbatim from `new-prompt.ts` into the Jumbo prompt. The `supabase` action type stays as part of the boltArtifact protocol even though database instructions are removed.

#### Scenario: Artifact instructions are identical
- **WHEN** comparing the `artifact_instructions` section of the Jumbo prompt to `new-prompt.ts`
- **THEN** the protocol rules, action type definitions, and ordering constraints SHALL be functionally identical

### Requirement: Jumbo prompt preserves WebContainer constraints
The `system_constraints` section documenting WebContainer limitations (no native binaries, no Git, limited Python, available shell commands) SHALL be preserved from `new-prompt.ts`.

#### Scenario: WebContainer constraints are present
- **WHEN** the Jumbo prompt is rendered
- **THEN** the `system_constraints` section SHALL list the same WebContainer limitations and available shell commands as `new-prompt.ts`

### Requirement: Jumbo prompt replaces mobile section
The `mobile_app_instructions` section SHALL replace React Native + Expo references with a concise note about Capacitor as the Vue-compatible mobile bridge, since mobile is out of scope for Phase 0.

#### Scenario: Mobile section references Capacitor
- **WHEN** the Jumbo prompt is rendered
- **THEN** the `mobile_app_instructions` section SHALL reference Capacitor instead of React Native/Expo and SHALL be concise since mobile is out of Phase 0 scope

### Requirement: Jumbo prompt includes Nuxt 4 example artifact with Tailwind Kompas theme
The `examples` section SHALL include at least one complete Nuxt 4 example demonstrating a `boltArtifact` with `package.json` (using `"nuxt": "^4.0.0"` and `@nuxtjs/tailwindcss`), `nuxt.config.ts` with `@nuxtjs/tailwindcss` module, `tailwind.config.ts` with Kompas token extensions, and a page component using `<script setup lang="ts">` and Tailwind utility classes with Kompas token names.

#### Scenario: Example demonstrates Nuxt 4 scaffolding with Tailwind Kompas theme
- **WHEN** the Jumbo prompt is rendered
- **THEN** the `examples` section SHALL contain a complete `boltArtifact` example that creates a Nuxt 4 app with a `tailwind.config.ts` extending the theme with Kompas tokens, and Vue SFC pages using Tailwind utility classes (not CSS custom properties)

### Requirement: Jumbo prompt does not include Supabase or database instructions
The `getJumboPrompt` function SHALL NOT accept a `supabase` parameter. The prompt SHALL NOT contain a `<database_instructions>` block. The `technology_preferences` section SHALL NOT mention Supabase as a database default.

#### Scenario: No supabase parameter
- **WHEN** `getJumboPrompt` is called from `PromptLibrary`
- **THEN** it SHALL accept only `cwd` (string, defaults to `WORK_DIR`) and `designScheme` (optional `DesignScheme`) — no `supabase` parameter

#### Scenario: No database instructions block
- **WHEN** the Jumbo prompt is rendered
- **THEN** it SHALL NOT contain a `<database_instructions>` section

#### Scenario: Technology preferences do not mention Supabase
- **WHEN** the Jumbo prompt is rendered
- **THEN** the `technology_preferences` section SHALL NOT contain "Use Supabase for databases by default" or any Supabase database recommendation
