# CLAUDE.md

Guidance for working in this repository.

## Project

**Erdem Family Weekly Planner** — a single-page, self-contained dashboard showing the family's
weekly routines and meals for Ece, Erdem, Arya, and Mira. There is no build step, framework, or
backend; everything lives in one file and runs directly in the browser.

## Structure

- `index.html` — the entire app: CSS in a `<style>` block, markup, and vanilla JS in a `<script>`
  block. **All edits happen here.**
- `README.md` — short project description.
- `.github/` — GitHub configuration.

There is **no package.json, no bundler, no test suite**. Do not introduce a build toolchain unless
explicitly asked.

## Running

Open the file directly in Google Chrome (per user preference, always use Chrome for local previews):

```bash
open -a "Google Chrome" index.html
```

No server is required.

## Architecture notes

- **Data model**: `initialData` is an array of 7 day objects, each with `day` (Turkish name),
  `engDay` (English name), `color` (CSS var), `meals` (string with `\n` separators), and
  `activities` (array of `{ time: "HH:MM", text }`).
- **Persistence**: state is saved to `localStorage` (`erdem_planner_data`). An optional MongoDB
  Data API endpoint can be configured at runtime (`erdem_mongo_config`); when set, data syncs via
  `fetchFromMongoDB` / `syncToMongoDB`. Local storage is the default ("Local Mock DB").
- **i18n**: the `translations` object holds `en` and `tr` strings. `setLanguage()` swaps all
  labels. Keep both languages in sync when adding UI text — there must be exactly **one**
  `const translations` declaration.
- **Clock**: BST time is derived with `Intl.DateTimeFormat` using the `Europe/London` timezone.
- **Reminders**: `checkUpcomingEvents()` (runs every 30s) shows the `#bst-banner` and fires a
  browser notification 10 minutes before any of today's activities. The `#bst-banner` element must
  exist in the DOM — the function reads it on load.
- **Person colors**: `getPersonClass()` maps activity text to `ece-act` / `erdem-act` / `arya-act`
  / `mira-act` classes, each styled with a distinct left border color.

## Conventions & gotchas

- The document must contain a **single** `<head>`/`<body>` and a single `<style>` block. Earlier
  edits accidentally duplicated the CSS and the `translations` object — duplicate `const`
  declarations are a fatal `SyntaxError` that stops the whole script and breaks page load. Verify
  with `grep -c "const translations" index.html` (should be `1`).
- Keep the layout responsive: `@media (max-width: 768px)` and `@media (max-width: 420px)` blocks
  collapse the grid to a single column and stack the action bar. Test changes at mobile widths.
- Use the existing CSS custom properties (`--accent-primary`, day colors, person colors) rather
  than hard-coding hex values.
- Activity times are 24-hour `"HH:MM"` strings; the reminder math depends on that format.

## Git

- Default branch: `main`.
- Commit/push only when asked. Use conventional commit prefixes (`feat:`, `fix:`) — matches the
  existing history.
