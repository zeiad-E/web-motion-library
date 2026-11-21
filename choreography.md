# Choreography: many effects, one experience

## The thread

Pick one element that carries the visitor through the page:
- **A travelling object** (3D product/mascot) that leaves the hero, turns to new angles per section, rides a path, then rests near the CTA.
- **A line** that draws from section to section.
- **A colour** that shifts with the story (light → night → light).
- **A shape** reused as mask, icon and portal (e.g. the logo's silhouette).

The thread makes ten sections feel like one.

## Handoffs

Each section should *end* with something the next one *starts from*:
- Object centres → becomes a portal → you're inside the next scene.
- Path ends at a milestone → next section's heading sits at that point.
- A card flies off → the next section's cards are already there.
Plan handoffs as a table: section · enters with · leaves with · thread state.

**Isolation is also a choice.** A strong standalone effect (e.g. a portal) can be clearer when the thread steps aside for it. Test both; don't force every effect to interact with the thread.

## Pacing

- Alternate loud and calm: cinematic hero → calm text → playful interaction → calm proof → signature → calm CTA.
- Pinned sections: 260–440vh. Shorter feels rushed; longer feels stuck. Show a progress rail.
- One idea per screen.

## Section edges

Decide per brand:
- **Sharp edges** (hard colour change at the section boundary): structured, confident, clearer chapters.
- **Soft blends** (gradients between sections): dreamy, continuous, but can look muddy or unfinished.
Ask the client; don't default to gradients everywhere.

## Hero entrance

1. Do heavy work first: build meshes (time-sliced), decode first images, warm up shaders with a tiny invisible draw.
2. Then run the text as an awaited sequence: eyebrow → headline (letter or word by letter, 40–60ms steps, frame-timed) → sub-phrase → paragraph → buttons.
3. Let the hero object's assembly overlap the headline so they feel like one moment; pieces appear staggered, never all at once.
4. Start any looping/rotating text only after the whole hero has settled.

## Mobile and accessibility

- Stack layouts: object above, text below; keep the CTA visible without scrolling.
- Replace hover-only effects with tap or idle animation.
- Reduced motion: keep the story, drop the spectacle (still frames, crossfades).
- Keep focus order and headings logical even when visuals are pinned or reordered.
