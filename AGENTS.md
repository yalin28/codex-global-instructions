# Global Codex Instructions

## Priority

These instructions merge Codex-oriented behavioral guidelines with the existing global Shopify theme component standards.

When instructions conflict, follow the Codex behavioral guidelines first. Treat the Shopify standards as domain-specific rules for Shopify theme work that apply after the higher-priority behavioral rules.

## Git Commit Attribution

When Codex helps complete a git commit, include the following commit message trailer so GitHub can attribute the commit as AI-assisted collaboration:

```text
Co-authored-by: Codex <noreply@openai.com>
```

Use this trailer for commits created by Codex unless the user explicitly asks not to include AI co-author attribution.

## Git Commit Message Style

When Codex writes a commit message, use a concise Conventional Commit-style subject:

```text
type(optional-scope): short imperative summary
```

Keep the subject focused on the actual change. Use the smallest accurate type:

- `init` (`initialize`): project bootstrap or initial scaffolding, for example `init: project bootstrap`
- `feat` (`feature`): new pages, modules, interactions, or capabilities, for example `feat(cart): add coupon support`
- `fix` (`fix`): bug fixes, broken logic, or production issues, for example `fix(login): token refresh error`
- `style` (`style`): UI, layout, color, spacing, or animation changes, for example `style(home): adjust banner spacing`
- `refactor` (`refactor`): structural changes that do not alter behavior, for example `refactor(api): simplify request wrapper`
- `docs` (`documentation`): README, comments, or other documentation changes, for example `docs: update local dev guide`
- `chore` (`chore`): build, configuration, dependency, or tooling changes, for example `chore: upgrade node to 20`
- `perf` (`performance`): explicit performance or bundle-size improvements, for example `perf(image): lazy load hero images`
- `test` (`test`): new or changed tests, for example `test(cart): add price calculation tests`

## Codex Agent Behavioral Guidelines

Behavioral guidelines to reduce common LLM coding mistakes in Codex agent workflows. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:

- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:

- "Add validation" -> "Write tests for invalid inputs, then make them pass"
- "Fix the bug" -> "Write a test that reproduces it, then make it pass"
- "Refactor X" -> "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:

```text
1. [Step] -> verify: [check]
2. [Step] -> verify: [check]
3. [Step] -> verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

## Shopify Theme Component Standards

Use these rules whenever you create or refactor Shopify theme sections, snippets, assets, or custom components. They generalize the custom component style found in projects such as `piscisea-website`, where existing examples may use project-specific prefixes like `cf-*`.

### Placement and file shape

- For new custom theme sections, prefer the `g-` prefix to mark generalized custom components unless extending an existing project-specific naming family.
- Treat existing prefixes such as `cf-`, `goc-`, or brand/project-specific names as local conventions to preserve when editing those files, not as the global default for new components.
- Keep each custom section in this order: Liquid pre-compute block, scoped `<style>`, section markup, local `<script>` when needed, then `{% schema %}`.
- Start by finding the closest existing section in `sections/` and mirror its structure, naming, responsive behavior, and schema organization.
- Keep edits scoped. Do not rename existing setting IDs, classes, block types, or schema IDs unless the task explicitly requires a migration.

### Change discipline

- When modifying existing files, make only the smallest change required to satisfy the request.
- Do not format unrelated code, restructure unrelated markup, rename unrelated classes/settings, or make opportunistic cleanups.
- Do not refactor existing component structure unless the requested behavior cannot be delivered safely without it.
- Do not introduce third-party libraries unless they are necessary for the requested feature and the project does not already provide a suitable pattern or dependency.

### Liquid conventions

- Begin sections with a `{%- liquid ... -%}` pre-compute block.
- Define `section_dom_id` from `section.settings.section_id`; fall back to the component prefix plus `section.id`, for example `g-feature-grid-{{ section.id }}`.
- Compute desktop and mobile spacing in Liquid before CSS. Use the existing spacing scale:
  - Desktop: `x-large` 120, `large` 80, `medium` 60, `small` 40, `none` 0, otherwise the custom default.
  - Mobile: `x-large` 80, `large` 64, `medium` 48, `small` 32, `none` 0, otherwise the custom default.
- Count renderable blocks before markup when block content is optional, usually with `valid_block_count`.
- Render optional content defensively with `!= blank`; do not output empty headings, media wrappers, buttons, or rich text containers.
- Escape plain text with `| escape`; allow rich text settings to render as rich text only when that is the intended editor behavior.
- Use Shopify media helpers (`image_url` + `image_tag`, `video_tag`) with explicit `class`, `widths`, `sizes`, `loading`, and video playback attributes as appropriate.
- Add `{{ block.shopify_attributes }}` to every rendered block root.

### CSS conventions

- Scope all CSS to the current section with `.component-name-{{ section.id }}` or the section DOM ID. Do not introduce unscoped selectors for custom components.
- Use BEM-like descendants under the scoped root, for example `.g-example-{{ section.id }}__inner`.
- Set root background and vertical padding from schema-driven values:
  - `background: {{ section.settings.background_color | default: 'transparent' }};`
  - `padding-top: {{ padding_top }}px;`
  - `padding-bottom: {{ padding_bottom }}px;`
- Use `box-sizing: border-box`, constrained `max-width`, and separate desktop/mobile side padding settings for content shells.
- Use `@media (max-width: 768px)` for mobile overrides and `@media (min-width: 769px)` for desktop-only behavior.
- Keep desktop and mobile typography, spacing, radius, aspect ratio, and layout values schema-driven when editors are expected to tune the section.
- Prefer real responsive layout rules over viewport-scaled font sizes. Avoid negative letter spacing unless matching an existing local section pattern exactly.
- Include `prefers-reduced-motion` handling for animated components.

### JavaScript conventions

- Use section-local scripts only when markup and CSS are not enough.
- Wrap scripts in an IIFE and resolve the current section by `document.getElementById({{ section_dom_id | json }})`.
- Guard every DOM query and return early when required nodes are missing.
- Keep selectors local to the section root and use `data-*` attributes for behavior hooks.
- For Swiper sections, wait for `window.Swiper` with bounded retry attempts, store the instance, update disabled nav state, and destroy/reinitialize only when necessary.
- For video sections, force muted/playsinline/loop attributes, catch `video.play()` rejections, pause inactive videos, and pause on `document.hidden`.
- Respect reduced motion when setting animation speed or transitions.

### Performance, semantics, and accessibility

- Prioritize performance for above-the-fold sections.
- Avoid layout shift, unnecessary JavaScript, render-blocking assets, and oversized media.
- Use explicit media dimensions, aspect-ratio boxes, or stable wrappers so images, videos, sliders, and dynamic text do not shift layout during loading.
- Use lazy loading for non-critical images, but do not lazy load the primary LCP image in an above-the-fold section.
- Keep the primary LCP media reasonably sized with appropriate `widths`, `sizes`, and responsive crops.
- Choose image `widths` and `sizes` based on the rendered image slot, not the source image size.
- For full-width hero or above-the-fold LCP images, include large widths such as `2400` or `3200` only when the image can actually render near full viewport width.
- For half-width media blocks, prefer responsive ranges such as `480, 720, 960, 1200, 1600, 2000`.
- For cards, thumbnails, small carousel items, icons, and compact media, avoid oversized widths; prefer ranges such as `240, 360, 480, 720, 960, 1200`.
- Always set a realistic `sizes` attribute that matches the layout, for example `(min-width: 990px) 50vw, 100vw` for a two-column image.
- Do not use `width: 3200` as a default. Use it only for full-bleed hero/LCP media or very large desktop presentation images.
- Use semantic HTML. Prefer `section`, `article`, `header`, `nav`, `ul/li`, `button`, and `a` according to their actual meaning.
- Do not create multiple `h1` elements unless the section is explicitly intended to render the page's primary heading.
- Buttons must be real `<button>` elements for actions; links must be `<a>` elements for navigation.
- Interactive elements must be keyboard accessible, have clear focus states, and expose appropriate ARIA state only when native semantics are not enough.

### Schema conventions

- New custom sections must include these baseline settings near the top of `settings`:
  - `section_id`
  - `background_color`
  - header `页面间距`
  - `padding_top`
  - `padding_bottom`
  - `padding_top_mobile`
  - `padding_bottom_mobile`
  - header `自定义间距`
  - `padding_top_default`
  - `padding_bottom_default`
  - `padding_top_mobile_default`
  - `padding_bottom_mobile_default`
- Use the exact spacing select labels and values already present in the closest mature custom section in the current project.
- Keep schema labels consistent with the existing bilingual style: Chinese for merchant-facing spacing/color groups, concise English names for section names and technical identifiers.
- Put layout settings before content styling settings, then media settings, then button/nav/interaction settings, then blocks and presets.
- Add `max_blocks` when the component has a natural content limit.

### Verification

- After changing Shopify Liquid, run Shopify theme validation when available. If no local validation command exists, at minimum inspect the changed section for Liquid/schema syntax, missing `endif/endfor`, unscoped CSS, empty optional output, and mobile breakpoint coverage.
- When adding or changing interactive UI, verify the behavior in the theme preview or browser if a preview server is available.
