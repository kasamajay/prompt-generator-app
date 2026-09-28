# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Overview

**Vibe Stack Builder** is a single-file static web app (`index.html`). The user picks a tech stack and the app generates a Markdown prompt for an AI coding assistant, in either "Build direct" or "Plan first" mode.

- Vanilla HTML, CSS and JS. No framework, no build step, no package.json, no tests.
- The only external dependency is the Iconify CDN script (`code.iconify.design/3/3.1.0`) for `logos:*`, `ph:*` and `simple-icons:*` icons.
- The folder is not a git repo (yet).

## Running / verifying

Open `index.html` in a browser, or run `python -m http.server 8000` and visit `http://localhost:8000`. There is no automated test suite, so verify changes manually:

1. Pick Web, then check that language, backend, frontend and database questions appear. Pick Desktop, then check that the desktop framework question replaces backend and frontend.
2. Check that the palette filters: languages by type, frameworks by the selected language.
3. Drag a card and click a card. Both should assign it, and the chip's × should remove it.
4. Toggle Build direct / Plan first and check the prompt text. Copy, Regenerate and Reset should all work.
5. Open the Tech Tree tab. Hover highlighting and tooltips should work, and the database grid should render.

## Code map (`index.html`)

Line numbers are approximate.

| Section | Lines | Notes |
|---|---|---|
| CSS tokens (`:root`) | ~10–33 | Dark theme only. Use these variables instead of hard-coded colours. |
| Component CSS | ~34–458 | Nav, description, panels, questions, palette, tech cards, tooltip, prompt, toast, tech tree. |
| Markup | ~461–583 | `#view-builder` and `#view-tree` views, toggled by `.tab-btn[data-view]`. |
| `TECH_DATA` | ~589 | `types`, `languages`, `backendFrameworks`, `frontendFrameworks`, `desktopFrameworks`, `databases`. |
| `QUESTION_DEFS` | ~674 | Ordered questions. `showWhen(state)` controls whether a question is shown. |
| `state` | ~686 | Single mutable state object. |
| Tooltip helpers | ~695 | `showTip`, `moveTip`, `hideTip` (a single body-level `#floatTip`). |
| Helpers | ~708 | `visibleQuestions`, `findTech`, `isDone`, `filterItem`. |
| Builder render | ~735 | `renderQuestions`, `renderTypeChoices`, `renderDropZone`, `renderPalette`. |
| Prompt generation | ~906 | `buildStackLines`, `generateDirectPrompt`, `generatePlanPrompt`, `renderPrompt`, `updateModeToggle`. |
| Tech Tree | ~1041 | `WEB_LANG_GROUPS`, `DESKTOP_LANG_GROUPS`, `buildAndRenderTree`, `highlightPath`, `clearHighlight`. |
| Toast / renderAll / events / init | ~1245–1311 | |

## Architecture

- **State → full re-render.** Every interaction mutates `state` and then calls `renderAll()`, which re-renders the questions, palette and prompt from scratch. There is no diffing. Keep it that way; the DOM is small.
- **Questions:** `kind:"type"` renders the Web/Desktop cards. `kind:"drop"` renders a drop zone bound to `state[q.id]`, and `q.category` names the `TECH_DATA` key the zone accepts.
- **Filtering (`filterItem`):** languages use `item.supports` (type). Frameworks use `item.languages` (selected language). Databases are never filtered.
- **Drag & drop:** the payload is `application/json` with `{id, category}`. A drop is rejected (with a red toast) if the categories don't match.
- **Prompts** are built line by line with `L.push(...)` and then `L.join("\n")`. Both modes share `buildStackLines()`.
- **Tech Tree** is built lazily, once, the first time the tab opens (`treeBuilt` flag). Nodes are absolutely positioned divs, and edges are an SVG overlay of cubic béziers. The layout constants are at the top of `buildAndRenderTree()`. The web branch uses `backendFrameworks` only.

## Common changes

- **Add a framework or database:** append to the matching `TECH_DATA` list as `{id, name, icon, description, languages?, fullstack?}`.
- **Add a language:** add it to `TECH_DATA.languages` with `supports`, reference it from the relevant frameworks' `languages`, **and** add it to `WEB_LANG_GROUPS` and/or `DESKTOP_LANG_GROUPS`, or it won't appear in the Tech Tree.
- **Add a question:** add it to `QUESTION_DEFS`, add its key to `state`, add a line in `buildStackLines()`, and clear it in the type-switch logic in `renderTypeChoices()` if it is type-specific. Reset already nulls every other key generically.
- **Change prompt wording:** edit `generateDirectPrompt()` or `generatePlanPrompt()`.

## Gotchas

- **Framework ids must not contain `-`.** Tree node ids are `${branch}-${group}-${fwId}`, and the tooltip recovers `fwId` with `n.id.split("-").pop()`.
- **Shared ids are intentional.** `nextjs`, `nuxt` and `sveltekit` exist in both `backendFrameworks` and `frontendFrameworks`. The fullstack note triggers on `state.backend === state.frontend`.
- The tree tooltip only looks up backend and desktop frameworks.
- Data is interpolated into `innerHTML` templates. That is fine for the static `TECH_DATA`, but never feed user input through those templates. The description textarea only flows into the prompt `<textarea>` via `.value`.
- The tree edge colours are hard-coded hex values (`#8b5cf6` / `#22d3ee`) that mirror `--purple` and `--cyan`. Update both if you change the palette.

## Conventions

- Keep the app a single self-contained `index.html`. Don't introduce build tooling or frameworks unless asked.
- Match the existing compact style: dense one-line CSS rules, `/* ─── SECTION ─── */` banners, and short `const` helpers.
- Icons: use Iconify ids (`logos:*` for brand logos, `ph:*-bold` for UI glyphs).
