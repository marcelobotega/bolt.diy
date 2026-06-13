## Context

bolt.diy exposes a prompt library (`app/lib/common/prompt-library.ts`) that maps string keys to prompt factories. The active prompt key is persisted in the user's settings and passed to the LLM at inference time. Adding a new prompt is purely additive: create a new TypeScript module exporting a factory function, then register it in `PromptLibrary.library`.

The existing baseline is `new-prompt.ts` (`getFineTunedPrompt`), a 305-line template string that already handles the `boltArtifact` protocol, design scheme injection, and WebContainer constraints. The Jumbo prompt is a fork of this file with targeted replacements in the technology, mobile, and design sections. This iteration removes all Supabase/database logic (this prompt targets simple Nuxt apps — landing pages, dashboards — not full-stack apps), upgrades from Nuxt 3 to Nuxt 4, and switches from CSS custom properties to a Tailwind CSS-based Kompas theme.

Kompas (`@kompas/ui`) is Jumbo's internal Vue component library hosted on a private registry. WebContainer cannot install private npm packages, so the Phase 0 experiment cannot use real Kompas components. The prompt instructs the LLM to approximate the Kompas look-and-feel by creating a `tailwind.config.ts` that extends Tailwind's default theme with the full Kompas token palette, using `@nuxtjs/tailwindcss` as the integration module. LLMs have deep familiarity with Tailwind's configuration format, producing better output than raw CSS custom properties.

`JUMBO_THEME_REFERENCE.md` (in the `research/` directory) is the single source of truth for all Kompas token values.

## Goals / Non-Goals

**Goals:**
- Provide a selectable prompt in bolt.diy Settings that steers the LLM toward Nuxt 4, Vue 3 Composition API, Pinia, and Kompas design tokens delivered via Tailwind CSS theme extension
- Keep the `boltArtifact` / `boltAction` protocol rules and WebContainer constraints intact — these are load-bearing and must not change
- Register the prompt with zero changes to the `PromptLibrary` public interface (additive only)
- Produce output that is immediately evaluable in the Phase 0 test scenarios (landing page, admin dashboard, contact form)
- Embed the complete Kompas token palette (all colors, button states, input states, banner colors, toggle switch colors, dark mode tokens, semantic mappings, typography, spacing, border radius, shadows, breakpoints, focus ring) in the Tailwind config instructions

**Non-Goals:**
- Installing or bundling the real `@kompas/ui` package (blocked by private registry + WebContainer)
- Supabase or any database integration (this prompt targets simple Nuxt apps, not full-stack)
- Automated test coverage for the prompt itself (Phase 0 is manual evaluation)
- Production deployment or CI integration
- Changes to the UI or settings persistence layer

## Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Base file | Fork `new-prompt.ts` | Preserves all `boltArtifact` protocol rules and WebContainer constraints that are battle-tested. Rewriting from scratch risks regressions in the artifact protocol. |
| Kompas token delivery | Tailwind CSS theme extension via `tailwind.config.ts` | LLMs know Tailwind deeply; generating via `extend` in Tailwind config produces better, more consistent output than raw CSS custom properties. The `tailwind.config.ts` serves as the single configuration point for all Kompas styling. No standalone `kompas-tokens.css` file. |
| Tailwind integration | `@nuxtjs/tailwindcss` Nuxt module | Official Nuxt module for Tailwind CSS. Handles PostCSS setup, HMR, and Tailwind content paths automatically. |
| Token completeness | Full palette from `JUMBO_THEME_REFERENCE.md` | Include ALL tokens: brand colors (Banana scale), greys (Oyster scale), semantic colors (Apple/Carrot/Tomato/Berry/Eggplant), neutrals, button colors, input states, banner colors, toggle switch, dark mode, semantic mappings, typography, spacing, border radius, shadows, breakpoints, focus ring. |
| Module name | `jumbo-prompt.ts`, export `getJumboPrompt` | Follows the naming convention of `new-prompt.ts` / `getFineTunedPrompt` and `prompts.ts` / `getSystemPrompt`. |
| Registration key | `jumbo` | Short, unambiguous, matches naming convention of `default`, `original`, `optimized`. |
| Mobile section | Replace React Native + Expo section with Capacitor note | Jumbo does not use React Native. Capacitor is the Vue-compatible mobile bridge. The section is kept concise since mobile is out of scope for Phase 0. |
| Supabase | Remove entirely | This prompt targets simple Nuxt apps (landing pages, dashboards, etc.), not full-stack apps. The `supabase` parameter is removed from `getJumboPrompt`. The entire `database_instructions` block is removed. "Use Supabase for databases by default" is removed from `technology_preferences`. The `supabase` action type in `artifact_instructions` stays (it is part of the boltArtifact protocol). |
| Target framework version | Nuxt 4 (`"nuxt": "^4.0.0"`) | Nuxt 4 is the current version in 2026. Examples and instructions target Nuxt 4 conventions. |
| Year | 2026 | The prompt states "The year is 2026". |
| Examples section | Include one complete Nuxt 4 counter-app example with Tailwind + Kompas theme | Demonstrates correct `<script setup lang="ts">`, Pinia store, `tailwind.config.ts` with Kompas tokens, and Tailwind utility classes throughout components. |

**Alternatives considered:**

- **Env-based prompt override** (rejected): Would require reading env vars at runtime and adding conditional logic to the loader. More complex and harder to test than a dedicated library entry.
- **Extend existing prompt with a mode flag** (rejected): Adds branching complexity to `new-prompt.ts`. A separate file keeps concerns isolated and makes the Jumbo customizations easy to read and iterate.
- **CSS custom properties for Kompas tokens** (rejected): The original approach used a `kompas-tokens.css` file with CSS custom properties. Switched to Tailwind CSS theme extension because LLMs generate better output with Tailwind's config format, and Tailwind utilities (`bg-kompas-primary`, `text-kompas-text-primary`, `rounded-kompas-md`) produce more consistent, readable component code.

## Risks / Trade-offs

| Risk | Mitigation |
|------|-----------|
| LLM ignores Nuxt/Vue instructions and falls back to React | Include explicit negative examples ("DO NOT use React, JSX, or `.tsx` files") in `technology_preferences` |
| Tailwind config drifts from real Kompas tokens | Pin exact hex values and spacing units from `JUMBO_THEME_REFERENCE.md` in the prompt; document the source of truth. The Tailwind config in the prompt is authoritative. |
| Forked file diverges from `new-prompt.ts` maintenance | Add a comment at the top of `jumbo-prompt.ts` citing the base version so future maintainers know what to diff |
| `boltArtifact` protocol regressions | Copy the `artifact_instructions` block verbatim — no paraphrasing |
| Phase 0 scope creep (building real Kompas integration) | The proposal explicitly scopes this to a validation experiment; the design doc repeats this boundary |
| Tailwind dependency adds project size | Acceptable for Phase 0. `@nuxtjs/tailwindcss` is lightweight and widely supported in WebContainer. |

## Open Questions

- **Which models should be tested first?** DeepSeek R2 and Claude Sonnet are candidates per `PHASE-0.md`. No design decision needed here — model selection is a runtime/settings choice.
- **Should the prompt include a Nuxt starter template entry in `app/utils/constants.ts`?** Marked optional in `ACTION-ITEMS.md`. Deferred: implement only if test scenarios reveal that the LLM consistently scaffolds a poor project root structure without it.
