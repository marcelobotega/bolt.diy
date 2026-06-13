## ADDED Requirements

### Requirement: Jumbo prompt is registered in the prompt library
The `PromptLibrary.library` map SHALL contain a `jumbo` entry that references the `getJumboPrompt` function from `jumbo-prompt.ts`, with label `Jumbo Nuxt 4 + Kompas` and a description indicating it targets Nuxt 4, Vue 3, and the Kompas design system via Tailwind CSS theme extension.

#### Scenario: Jumbo entry appears in the prompt list
- **WHEN** `PromptLibrary.getList()` is called
- **THEN** the returned array SHALL include an entry with `id: 'jumbo'`, `label: 'Jumbo Nuxt 4 + Kompas'`, and a non-empty description

#### Scenario: Jumbo prompt is retrievable by key
- **WHEN** `PromptLibrary.getPropmtFromLibrary('jumbo', options)` is called with valid `PromptOptions`
- **THEN** it SHALL return the string produced by `getJumboPrompt(options.cwd, options.designScheme)` — note: no `options.supabase` argument

#### Scenario: Existing prompts are unaffected
- **WHEN** `PromptLibrary.getList()` is called
- **THEN** the `default`, `original`, and `optimized` entries SHALL still be present and unchanged
