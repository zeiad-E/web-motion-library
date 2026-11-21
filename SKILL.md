---
name: web-motion-library
description: Use when planning, brainstorming or building websites with strong animation, scroll effects, 3D/WebGL, micro-interactions or "wow" motion; when asked for many animation ideas (e.g. "give me 50/100 ideas"), a hero concept, a scroll story, or which effects suit an audience; when choosing GSAP, Lenis, Three.js, shaders, Lottie/Rive or View Transitions; or when motion stutters, jumps or lags.
---

# Web Motion Library

A reference library of web motion: ~100 effects with what they do to an audience, how to build them, what they cost, plus an idea engine, choreography rules and field-tested recipes.

**Live reference implementations** (plain HTML/CSS/JS, 30 effects running plus two full home pages) are in the BrightSmile repo, folder `frontend-drafts/` (github.com/zeiad-E/brightsmile-clinic-assistant; locally `D:\Work\Agent\frontend-drafts`). Open them when you need working code for an effect in the catalog.

**Core principle:** strong motion work is *one concept* told through *a few effects that hand off to each other*, with *one signature moment*, built so it stays smooth on a mid-range phone. A list of nice effects is not a design.

## Files

| File | Use it for |
|---|---|
| `catalog.md` | The effect library: 12 families, each effect with what the visitor sees, how it's built, cost, audience impact, pitfalls |
| `audience.md` | What motion does to people; fit by industry and audience; when motion hurts |
| `ideas.md` | The idea engine: how to produce 20–100+ strong, varied ideas, scoring, output format |
| `choreography.md` | Turning effects into one page: thread object, handoffs, pacing, section edges, hero entrance |
| `tools.md` | Stack choices (GSAP, Lenis, Three.js, raw WebGL, CSS scroll timelines, View Transitions, Lottie/Rive, AI asset pipeline) and rough KB/ms budgets |
| `recipes.md` | Working code patterns (pinning, smooth scroll, letter reveal, image sequence, portal, X-ray, goo, shaders, assembling object, RTL, path morph) and the performance lessons that made them smooth |

## Workflow

1. **Brief first.** Pin down: audience (who, how anxious/excited, phone share, language/RTL), goal (trust, sales, booking, delight), brand mood, budget. Read `audience.md` for the fit.
2. **Concept before effects.** Write one sentence the whole site enacts ("the visitor boots up an agent", "you step inside the clinic", "the product assembles itself"). Pick a *thread*: an object, line or colour that travels through the page.
3. **Generate wide** with `ideas.md` (trigger × subject × transformation × narrative), across *all* catalog families, not just reveal/fade.
4. **Score and cut**: impact vs effort vs audience fit. Keep 1 signature moment, 3–6 supporting effects, micro-interactions everywhere.
5. **Choreograph** with `choreography.md`: order, handoffs, pacing (loud → calm → loud), section edges.
6. **Build** with `tools.md` + `recipes.md`; prototype hard parts in plain HTML first.
7. **Verify** on a mid-range Android, with reduced motion on, keyboard only, and in RTL if relevant.

## Non-negotiables

| Rule | Why |
|---|---|
| One signature 3D/cinematic moment per page | Stacked spectacles cancel each other and kill phones |
| Animate only `transform`, `opacity`, `filter` (sparingly), canvas/WebGL | Layout properties stutter |
| Never read layout (`getBoundingClientRect`) every frame; measure on resize | Forced reflow = jank |
| Drive sequences step by step (await each step), never by long fixed CSS delays | After any main-thread stall, delayed transitions all fire at once |
| Heavy work (mesh building, image decoding, shader compile) before or between animations, time-sliced | Otherwise the first seconds jump |
| No `backdrop-filter` above an animated canvas | The browser re-blurs every frame |
| Full-screen canvas: cap device pixel ratio at 1.5–2 | 4K-wide canvases melt laptops |
| `prefers-reduced-motion`: still hero, simple fades, no smooth-scroll hijack | Accessibility and vestibular safety |
| All text is real HTML (never baked into frames/textures) | Readable, translatable, searchable |
| Every hover-only reveal also works with keyboard focus and touch | Most visitors are on phones |
| Arabic/RTL: mirror directions (slides, marquees, progress) | Motion direction is part of reading direction |

## Common mistakes

- Giving a list of safe UI niceties (accordion, skeleton, fade-up) and calling it a motion concept. Those are baseline polish; also deliver the concept and signature moment.
- Ideas without cost or audience impact: every idea needs *why it works for this audience* and *what it costs*.
- Ten wow effects on one page. Pick one; let the rest serve it.
- Smooth soft gradients between every section when the brand wants crisp structure. Decide edges deliberately (`choreography.md`).
- Trusting a hidden/background browser tab for timing: rAF pauses there. Test visible, or with a timer-driven rAF override for automation only.
