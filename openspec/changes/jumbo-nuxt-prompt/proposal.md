## Why

bolt.diy currently generates React + Vite code by default, but Jumbo's frontend standard is Nuxt 4 + Vue 3 + Kompas design system. Without a Jumbo-specific system prompt, every LLM-generated scaffold requires significant manual rewriting to align with the team's actual tech stack — slowing down the Phase 0 validation experiment and producing misleading results.

## What Changes

- **Modified file** `app/lib/common/prompts/jumbo-prompt.ts` — update the Jumbo-specific system prompt to target Nuxt 4 (not Nuxt 3), remove all Supabase/database references, and switch from CSS custom properties (`kompas-tokens.css`) to a Tailwind CSS-based Kompas theme via `tailwind.config.ts` extending Tailwind's default theme with the full Kompas token palette from `JUMBO_THEME_REFERENCE.md`
- **Modified file** `app/lib/common/prompt-library.ts` — update the `jumbo` registration: remove `options.supabase` from the `getJumboPrompt` call, update label/description to reference Nuxt 4
- All existing prompts (`default`, `original`, `optimized`) remain unchanged — no breaking changes

## Capabilities

### New Capabilities

- `jumbo-system-prompt`: A complete LLM system prompt that steers code generation toward Nuxt 4 + Vue 3 + Kompas, including Vue SFC syntax, Pinia state management, file-based routing, Kompas design tokens delivered as a Tailwind CSS extended theme (colors, spacing, typography, border radius, shadows, breakpoints), and one complete Nuxt app scaffold example

### Modified Capabilities

- `prompt-library-registration`: The `PromptLibrary.library` map's `jumbo` entry is updated: `getJumboPrompt` no longer accepts a `supabase` parameter, label updated to `Jumbo Nuxt 4 + Kompas`

## Impact

- **Modified file:** `app/lib/common/prompts/jumbo-prompt.ts` (remove Supabase, target Nuxt 4, switch to Tailwind-based Kompas theme)
- **Modified file:** `app/lib/common/prompt-library.ts` (update call site — drop `options.supabase`)
- **No API changes** — the `PromptLibrary` interface is unchanged; no route, loader, or action is affected
- **No new runtime dependencies** — the prompt is a pure TypeScript string template
- **WebContainer constraint:** Kompas (`@kompas/ui`) is a private package and cannot be installed in WebContainer; the prompt instructs the LLM to create a `tailwind.config.ts` that extends Tailwind's theme with Kompas design tokens, using `@nuxtjs/tailwindcss` as the Nuxt module
- **Scope:** Phase 0 validation experiment only — not production; output quality is evaluated manually against test scenarios
