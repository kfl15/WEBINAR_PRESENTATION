# Token Cost Memory Optimization Webinar Pack

Prepared for a beginner-friendly developer webinar on August 15, 2026.

## Focus

This pack is about developer-side memory optimization for API-based LLM systems:

- Chatbots
- Websites
- Customer-support systems
- Internal tools
- Document Q&A apps

The main idea is to control what the application sends to the LLM API, how much the model generates, and when an API call can be avoided.

## Files

- `slides.html` - browser-ready 17-slide presentation.
- `developer_side_memory_optimization.pptx` - editable PowerPoint version of the slide deck.
- `build_pptx.py` - generator script used to create the PowerPoint file.
- `speaker_notes.md` - full speaking notes, scripts, examples, case study, and Q&A.
- `cheat_sheet.md` - one-page revision sheet for fast review.
- `study_schedule.md` - same-day preparation and rehearsal plan.
- `sources.md` - references grouped by technique.

## How To Present

Open `slides.html` in a browser.

Controls:

- Right arrow / Space: next slide
- Left arrow: previous slide
- N: show or hide speaker notes
- Home: first slide
- End: last slide

## Recommended Workflow Today

1. Read `study_schedule.md`.
2. Read `cheat_sheet.md`.
3. Read `speaker_notes.md` once.
4. Open `slides.html` and rehearse with a timer.
5. Review the advanced names slide and sources only if you need confidence for Q&A.

## Main Message

Memory optimization is application design: keep the right information in the cheapest useful place, and avoid the LLM call when the app already knows the answer.
