## 1. Update Jumbo prompt file

- [x] 1.1 Update `getJumboPrompt` function signature in `app/lib/common/prompts/jumbo-prompt.ts`: remove the `supabase` parameter entirely (keep `cwd` and `designScheme`)
- [x] 1.2 Update file header comment to reference Nuxt 4 instead of Nuxt 3
- [x] 1.3 Change "The year is 2025" to "The year is 2026" in the prompt string
- [x] 1.4 Update `technology_preferences` section: replace all "Nuxt 3" references with "Nuxt 4"; remove the line "Use Supabase for databases by default. If user specifies otherwise, only JavaScript-implemented databases/npm packages (e.g., libsql, sqlite) will work"
- [x] 1.5 Remove the entire `<database_instructions>` block (the Supabase conditional logic and all database instructions — approximately lines 80-165). This prompt targets simple Nuxt apps, not full-stack.
- [x] 1.6 Replace the Kompas mock strategy in `<design_instructions>`: remove the `kompas-tokens.css` CSS custom properties approach and replace with Tailwind CSS theme extension instructions. The prompt SHALL instruct the LLM to: (a) install Tailwind CSS via `@nuxtjs/tailwindcss` Nuxt module, (b) create a `tailwind.config.ts` that extends Tailwind's default theme with the full Kompas token palette, (c) use Tailwind utility classes throughout components (e.g., `bg-kompas-primary`, `text-kompas-text-primary`, `rounded-kompas-md`), (d) NOT create a standalone `kompas-tokens.css` file
- [x] 1.7 Embed the complete Kompas token palette in the Tailwind config instructions, sourced from `JUMBO_THEME_REFERENCE.md`. Include ALL tokens: brand colors (full Banana scale), greys (full Oyster scale), semantic colors (Apple green, Carrot orange, Tomato red, Berry blue, Eggplant purple), neutrals (Milk/White, Olive/Black), button colors (primary/secondary/positive/disabled with hover/active states), input states (default/hover/error borders), banner colors (7 types with light/dark variants), toggle switch colors (off/on/handle), dark mode tokens (backgrounds, text, borders, inputs, panels), semantic mappings (text roles, background roles, border roles), typography (Jumbo TheSans font family, sizes 12-64px, weights 400/700/900, line heights 1.2/1.5), spacing (full Kompas scale: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 64, 80), border radius (4, 8, 12, 16, pill/100px), shadows (small and medium), breakpoints (XS 375, S 576, M 768, L 992, XL 1200, XXL 1600), focus ring (#0A4FFF — do not override with brand colors)
- [x] 1.8 Update the `<examples>` section: change the example to use Nuxt 4 (`"nuxt": "^4.0.0"`), include `@nuxtjs/tailwindcss` in modules, include a `tailwind.config.ts` with Kompas token extensions, use Tailwind utility classes in the Vue SFC template instead of CSS custom properties, remove `kompas-tokens.css`
- [x] 1.9 Verify `artifact_instructions` still contains the `supabase` action type (it is part of the boltArtifact protocol and stays)
- [x] 1.10 Verify `system_constraints`, `vue_state_management`, `vue_routing`, and `mobile_app_instructions` sections are intact and reference Nuxt 4 where applicable

## 2. Update prompt library registration

- [x] 2.1 In `app/lib/common/prompt-library.ts`, update the `jumbo` entry: change label to `Jumbo Nuxt 4 + Kompas`, update description to reference Nuxt 4 and Tailwind-based Kompas theme
- [x] 2.2 Update the `get` function call: change from `getJumboPrompt(options.cwd, options.supabase, options.designScheme)` to `getJumboPrompt(options.cwd, options.designScheme)`

## 3. Verify

- [x] 3.1 Run `pnpm typecheck` to confirm no TypeScript errors after removing the `supabase` parameter
- [x] 3.2 Run `pnpm lint` to confirm no linting violations
