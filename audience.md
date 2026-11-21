# Audience: what motion does to people

## Why some motion grabs attention

| Driver | What triggers it | Strongest effects |
|---|---|---|
| **Agency** ("I caused that") | The visitor's scroll/drag/cursor visibly drives something | Image-sequence scrub, before/after drag, particle push, liquid ripple, X-ray lens |
| **Anticipation** | Something is clearly about to happen | Assembling object, boot counter, drawing path, pinned beats with a progress rail |
| **Curiosity** | Something is hidden and partly revealed | X-ray/flashlight, portal zoom, exploded layers, scramble text |
| **Reward** | Every small action gets a response | Magnetic buttons, hover previews, success morphs, ripple feedback |
| **Story** | Motion has a beginning, middle and end | Journey path, scroll chapters, travelling companion object |
| **Personality** | One idea only this brand would do | Keychain nav, retro computer, mascot that turns to look at the chat |
| **Restraint** | Calm sections between loud ones | Big type + whitespace after a cinematic hero |

Award reviewers and real users reward a *personal idea* over technical density. Sites win on "the one where you fly into the tooth", not "the one with 20 effects".

## Fit by audience / industry

| Audience | Mood | Lean into | Avoid |
|---|---|---|---|
| Medical, dental, clinics | Calm, trust, clarity | Assembling/clean object, before/after, journey path, gentle scroll story, live assistant, X-ray curiosity | Glitch, boot loaders (waiting), fast flashes, aggressive marquees |
| Legal, finance, insurance | Seriousness, fairness, safety | Process timelines that draw, calm reveals, count-up proof, comparison toggles, a single dignified signature (e.g. scales assembling, document unfolding in 3D) | Playful physics, sparkle trails, neon |
| Luxury, real estate, hospitality | Desire, space, slowness | Depth-map photos, slow Ken Burns, glass/chrome objects, portal into a property, ribbon galleries | Busy UI, bouncy springs |
| SaaS / B2B / AI products | Competence, speed, "it's real" | Exploded architecture, live product demo, particle morphs, bento micro-motion, scramble/decode text | Vague abstract blobs with no meaning |
| E-commerce / product | Desire, detail, confidence | Product assembling/exploding, 3D turntable, colour swap morphs, scroll scrub through features | Effects that slow checkout |
| Food & beverage | Appetite, freshness | Pour/splash sequences, liquid ripple, ingredient assembly | Cold tech aesthetics |
| Kids, education | Play, encouragement | Mascots, sparkle trails, drag & throw, rewards, click-to-advance stories | Dark cinematic heaviness |
| Portfolio / creative / agency | Personality, craft | Custom cursors, giant type, WebGL signature, page wipes, seamless loops, ASCII | Template look |
| Events, music, youth brands | Energy | Velocity marquees, kinetic type, glitch, anti-grid | Long calm sections |
| Government / NGO / health info | Clarity, inclusion | Minimal: reveals, progress, step diagrams | Anything that hides content |

## When motion hurts

- **Anxious visitors** (patients, legal clients): slow ease-out, < 600ms per UI move, no surprise motion near forms.
- **Waiting**: preloaders that exist only for show lose service-business visitors. Hide real loading only.
- **Phones on 4G**: heavy frames/3D before content = bounce. Show the first frame instantly.
- **Motion sensitivity**: parallax, zooms and spins can cause nausea; `prefers-reduced-motion` must reduce them.
- **Reading pages** (articles, docs, policies): keep motion out of the reading column.
- **Conversion points**: forms and checkouts stay still and fast; motion only confirms success.

## Arabic / RTL audiences

- Mirror horizontal motion (slides, marquees, progress fills, journey directions).
- Split-letter effects break Arabic letter joining: animate by *word*, not letter, for Arabic text.
- Pick fonts with strong Arabic (Cairo, Tajawal, IBM Plex Sans Arabic, Noto Kufi) and test long strings.
