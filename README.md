# 🌸 Bloom — Milestone Tracker

A gentle, offline-capable PWA for tracking a little one's development from **6 months to 3 years** — built with love, in a single file.

## What it does

- **Six stages** (6–9, 9–12, 12–18, 18–24, 24–30, 30–36 months), each with:
  - **Milestones** — CDC-aligned checklists across six domains: Moving, Hands, Talking, Thinking, Social, and Everyday skills.
  - **Learn & play** — matching activities: daily reading, movement play, fine motor, and pretend & thinking.
- **Multiple children** — switch between little ones, each with their own progress and colour.
- **A "Now" ruler** — a timeline with a NOW marker that lands on your child's current stage, based on their birthdate.
- **Saves automatically** — everything you check off is stored on the device. Gentle celebrations when a domain or whole stage is complete.
- **Per-stage notes** and **JSON backup** (export / import / reset).

## Run it locally

Service workers need HTTP (not `file://`):

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

Add it to your phone's home screen for a full-screen, offline app.

## Tech

One `index.html` (markup + CSS + JS), a network-first `sw.js` service worker, a web `manifest.json`, and two icons. No build step, no dependencies, no tracking — all data stays on the device.

## A note

Bloom is a loving keepsake and encouragement tool, **not medical advice**. Every child grows at their own pace; milestone ranges follow the CDC's "Learn the Signs. Act Early." program. If you ever have concerns, talk with your pediatrician.
