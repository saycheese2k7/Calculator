# Copilot / AI Agent Instructions

This repository is a tiny static calculator web app. The guidance below is focused and actionable for contributors or AI agents working directly in this codebase.

- **Project type:** Single-page static site (no build tools, no package manager). Main files:
  - [calculator.html](calculator.html) — app UI and the only JS (inline) currently.
  - [style.css](style.css) — layout and styling.
  - [script.js](script.js) — present but empty; used as the canonical place to extract JS logic if refactoring.

- **Big picture / architecture:**
  - Minimal, client-side only. All runtime behavior lives in global JS functions declared in `calculator.html`'s inline <script> tag.
  - UI buttons use inline `onclick` attributes to call global functions (e.g., `equal()`, `del()`), and many append characters directly to the `output` input value.

- **Key patterns to preserve when editing:**
  - Keep functions on the global scope or migrate all inline `onclick` handlers to programmatic listeners in one commit (avoid half-migration).
  - The `output` field (id="output") is the single source of truth for the current expression/value — most code reads/writes it directly.
  - `eval()` is used by `equal()` to compute expressions. If replacing `eval()` with a parser, preserve existing behavior for operators `+ - * / %` and numeric literals (tests/manual verification required).

- **Files to inspect / update for changes:**
  - [calculator.html](calculator.html) — UI and current behavior; update this when adding/removing buttons or changing inline handlers.
  - [script.js](script.js) — preferred target for moving inline JS. If you move code here, also update `calculator.html` to include the script and remove duplicated inline code.

- **Developer workflows / commands:**
  - No build step. To view locally, serve the folder and open a browser. Example quick server:

    ```bash
    python3 -m http.server 8000
    # then open http://localhost:8000/calculator.html
    ```

- **Testing & debugging notes:**
  - There are no automated tests. Use the browser console to inspect `console.log` output (e.g., `equal()` logs results).
  - When changing event wiring, test every button (numbers, operators, DEL, AC, =). Verify edge cases: consecutive operators, leading zeros, decimal usage, and percent `%` semantics.

- **Safety and behavioral cautions for AI edits:**
  - Do not remove or silently change `eval()` without explicit instruction — it changes behavior significantly. If proposing a safer alternative, include unit tests or a short migration plan and keep old behavior behind a feature flag or comment.
  - Many `onclick` attributes call `getElementById('output')` without `document.` — this looks like a bug. If you change these occurrences, update them consistently across all button attributes or centralize handlers in `script.js`.

- **Small refactor suggestions (explicit, safe):**
  - Move all JS into `script.js`, wrap code in an IIFE or `DOMContentLoaded` handler, and replace inline `onclick` with `addEventListener` in a single commit.
  - If adding new UI states, prefer updating `calculator.html` and adjusting CSS selectors in `style.css`.

- **Examples from codebase:**
  - `equal()` (in `calculator.html`) uses `let sum = eval(sumText); document.getElementById('output').value = sum;` — preserve operator support and console logging when changing this.
  - `del()` slices the current string: `str.slice(0, -1)` — keep this behavior when refactoring.

If anything here is unclear or you'd like more detailed rules (e.g., a migration plan to remove `eval()`), tell me which parts to expand and I will iterate.
