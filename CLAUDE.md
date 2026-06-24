# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

**Bloom** is a child development milestone tracker built as an offline-capable PWA, made for a parent to use on her phone (installed to the home screen via "Add to Home Screen"). It tracks development from **6 months to 3 years**, organized as six stages — 6–9, 9–12, 12–18, 18–24, 24–30, 30–36 months.

Each stage has two faces:
- **Milestones** — CDC-aligned checklists across six domains: **Moving** (gross motor), **Hands** (fine motor), **Talking** (language), **Thinking** (cognitive), **Social** (social/emotional), and **Everyday skills** (self-help). Content is mapped from the CDC "Learn the Signs. Act Early." 2022 milestone checklists, each stage anchored to the CDC checkpoint at its upper bound (9/12/18/24/30/36 months).
- **Learn & play** — a matching activity curriculum in four groups: Daily reading, Movement play, Fine motor, and Pretend & thinking.

The app supports **multiple children** (a horizontal child switcher with colored avatars). The **Now** view shows a six-segment "ruler" timeline with a **NOW marker** positioned by the active child's age, highlighting their current stage. Checking off any milestone or activity saves instantly. Completing a whole domain triggers a gentle celebration; completing every milestone in a stage triggers a bigger confetti celebration (`celebrate()` / `confettiBurst()`).

## Structure

There is no build system, no dependencies, no tests. The entire app is **one file**:

- `index.html` — all markup, CSS, and JavaScript. CSS is in a `<style>` block, app logic in a `<script>` block at the bottom. The milestone/activity content lives in the `STAGES` constant.
- `sw.js` — service worker. Network-first with cache fallback so the app works offline and updates arrive when online.
- `manifest.json` — PWA manifest (standalone, theme color `#171320`).
- `icon-192.png` / `icon-512.png` — app icons (soft daisy on a rose→lavender gradient).

## Running / testing changes

Serve the folder over HTTP (service workers don't register from `file://`):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

After editing, hard-refresh; the service worker is network-first so changes show up on reload. If caching gets sticky, bump the `CACHE` version suffix in `sw.js` (currently `bloom-v4`) to force old caches to purge on activate.

## Key implementation details

- **Data model:** all state lives in `localStorage` under the key `bloom`:
  ```js
  { v:1, activeChild:"<id>", children:{ "<id>": {
      name, birth /* ISO date */, color /* rose|peach|sage|lav|sky|butter */,
      checks:{ "<itemId>": 1 }, notes:{ "<stageId>": "…" } } } }
  ```
  `checks` holds both milestone and activity ids. **Item ids are stable strings** derived from content order: milestones `m:<stageIdx>:<domainKey>:<i>`, activities `a:<stageIdx>:<groupKey>:<i>`. Keep `STAGES` item order stable or you'll shift saved checks. `load()` has a migration guard that normalizes shape and back-fills missing fields — extend it (don't break it) if the shape changes.
- **Age / stage math:** `ageMonths(birth)` returns a fractional age. `stageOf(m)` picks the stage; `markerPct(m)` maps age onto the six equal-width ruler segments for the NOW pin. There is no hardcoded date anchor — everything derives from each child's `birth`.
- **Rendering:** `render()` re-renders the visible view from `STAGES` + the active child's `checks`. There's no framework — keep it that way unless asked. Section expand/collapse state lives in `openSecs`.
- **Content:** edit the `STAGES` array to change milestones or activities. Each **milestone is an object** `{t, look, tip}` — `t` is the checklist text, `look` ("what it looks like") and `tip` ("if not yet, try") fill the tap-to-expand info panel; **activities remain plain strings**. Tapping a milestone's body toggles `openInfo[id]` (the expander); the separate `.check` zone toggles the check, so the two taps never collide. `DOMAINS` and `GROUPS` define the section metadata (label, emoji, accent hue).

## Constraints

- **Don't break existing saved data.** Any change to the data shape needs a migration in `load()` (follow the existing normalization pattern). Don't reorder `STAGES` items without remapping `checks`.
- Phone-first: used on a mobile screen. Check narrow-viewport layout and `env(safe-area-inset-*)` when touching CSS.
- Keep it a single self-contained `index.html` — no build step, no npm.
- This is an encouragement/keepsake tool, **not medical advice** — keep the gentle CDC-referencing disclaimer in Settings.

## Deployment

Intended to deploy via GitHub Pages from the repo root (same model as the companion workout-app), but **deployment is intentionally not set up yet**. To deploy once ready: push to the default branch and enable Pages. When shipping user-visible changes, bump the `CACHE` version in `sw.js` so installed phones fetch the new files.
