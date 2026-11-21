# Recipes and field lessons

Plain-JS patterns that worked in production prototypes. Adapt; don't paste blindly.

## Performance lessons (each one caused visible stutter before it was fixed)

| Symptom | Cause | Fix |
|---|---|---|
| Letters appear "Y… our s… mile," unevenly | Heavy JS chunks (mesh build) ran between letter timers | Start text after heavy work; step letters with rAF timing relative to the previous letter |
| Half the headline appears at once after a pause | Fixed CSS `transition-delay`s all expired during a main-thread stall | Drive sequences with `await` steps, not long CSS delays |
| First second of a WebGL hero freezes | Shader compiled lazily on first draw (Chrome/Windows ANGLE) | Warm up: draw one zero-size triangle and `readPixels` 1px before animating |
| Page frozen 1–3 s on load | Synchronous geometry build | Generator + 8ms time slices (`setTimeout` pump); avoid `Math.hypot`, `**`, array spreads in hot loops |
| Hero stutters on big screens | Full-screen canvas at DPR 2 (≈4K wide) | Cap DPR at 1.5 for full-screen canvases |
| Constant jank over a canvas | `backdrop-filter` elements above an animating canvas | Use solid/translucent backgrounds there |
| Choppy scroll-linked object | `getBoundingClientRect` on many elements every frame | Measure once on resize/fonts-ready; compute sticky positions with math |
| Pieces all pop in together | Every piece visible from t=0 | Per-piece appear time following flight order; grow from small |
| Pieces jolt when leaving | Ease-out from full speed | Smootherstep `k³(k(6k−15)+10)`: zero speed at start and end |
| Mint stripes across a smooth mesh | Flipping normals with `gl_FrontFacing` on an unoriented mesh | Use field-derived normals as-is |
| Page wider than the phone | `aspect-ratio` + `min-height` forces width | Drop min-height or change ratio on mobile |
| rAF animations "don't run" in automation | Hidden/background tab pauses rAF | Test visible; for automation only, swap rAF for a timer behind a URL flag |

## Pinned progress (no library)

```js
// 0 when the sticky panel locks, 1 when it lets go
const pinProgress = s => { const r = s.getBoundingClientRect(); return Math.min(1, Math.max(0, -r.top / (r.height - innerHeight))); };
// sticky panel's top on screen from cached numbers (no layout reads per frame)
const pinTopScreen = (absTop, h) => Math.min(Math.max(absTop - scrollY, 0), absTop + h - innerHeight - scrollY);
```
HTML: `<section style="height:300vh"><div style="position:sticky;top:0;height:100vh">…</div></section>`

## Smooth scroll in ~20 lines (prototype; use Lenis in production)

```js
let target = scrollY, current = scrollY, running = false;
addEventListener('wheel', e => { e.preventDefault(); target = Math.max(0, Math.min(target + e.deltaY, document.documentElement.scrollHeight - innerHeight)); if (!running) { running = true; requestAnimationFrame(tick); } }, { passive: false });
addEventListener('scroll', () => { if (!running) target = current = scrollY; });
function tick() { current += (target - current) * .09; if (Math.abs(target - current) < .4) { current = target; running = false; } scrollTo(0, current); if (running) requestAnimationFrame(tick); }
```

## Frame-stepped letter reveal

```js
async function typeIn(letters, gap = 50) {
  await new Promise(done => { let i = 0, due = 0;
    (function step(now) { if (now >= due) { letters[i++].classList.add('on'); due = now + gap; }
      i < letters.length ? requestAnimationFrame(step) : done(); })(performance.now()); });
}
// CSS: .c{opacity:0;transform:translateY(.35em);transition:opacity .5s,transform .6s cubic-bezier(.2,.8,.2,1)} .c.on{opacity:1;transform:none}
```

## Image-sequence scrub

```js
const frames = []; // Image objects; load first 10 eagerly, the rest lazily
function draw(p) { const i = Math.round(p * (frames.length - 1)), img = frames[i]; if (!img?.complete) return;
  const s = Math.max(W / img.width, H / img.height); ctx.drawImage(img, (W - img.width * s) / 2, (H - img.height * s) / 2, img.width * s, img.height * s); }
// call draw(pinProgress(section)) from a rAF loop only when progress changed
```

## Magnetic button

```js
addEventListener('pointermove', e => { const b = btn.getBoundingClientRect(), dx = e.clientX - (b.left + b.width / 2), dy = e.clientY - (b.top + b.height / 2), d = Math.hypot(dx, dy);
  btn.style.transform = d < 140 ? `translate(${dx * .3}px, ${dy * .3}px)` : ''; });
```

## Portal (fly through a shape)

SVG over the next scene: `<mask>` with a white rect and the shape in black; a navy rect uses the mask. Scroll sets
`transform="translate(cx cy) scale(s) translate(-ax -ay)"` with `s = s0 * Math.pow(56, Math.pow(p / .85, 1.5))`; scene behind scales `1.28 → 1`.

## X-ray lens

Two layers with identical geometry; the top one: `mask-image: radial-gradient(circle var(--r) at var(--x) var(--y), #000 96%, transparent 100%)`; lerp `--x/--y` toward the pointer; wander on idle.

## Gooey menu

```html
<filter id="goo"><feGaussianBlur in="SourceGraphic" stdDeviation="10" result="b"/>
<feColorMatrix in="b" values="1 0 0 0 0 0 1 0 0 0 0 0 1 0 0 0 0 0 22 -10" result="g"/><feComposite in="SourceGraphic" in2="g" operator="atop"/></filter>
```
Blobs on a layer with `filter:url(#goo)`; icons on a separate unfiltered layer at the same positions.

## Raw WebGL full-screen shader (depth map, ripple, gradients)

One quad (`[-1,-1, 1,-1, -1,1, 1,1]`, TRIANGLE_STRIP), `v_uv = p*.5+.5`, textures with `UNPACK_FLIP_Y_WEBGL`, `CLAMP_TO_EDGE`, `LINEAR`. Cover-fit: `scale = aspect > texAspect ? [1, texAspect/aspect] : [aspect/texAspect, 1]`; `uv = (v_uv-.5)*scale+.5`.
- **Depth map**: `d = texture(depth, uv).r; uv2 = uv + mouse*(d-.4)*.05;` look up depth again at `uv2` and recompute (smoother edges); blur the depth map ~10px.
- **Ripple**: for each of N rings `(x, y, t0, strength)`: `ring = d - age*.32; w = sin(ring*70)*exp(-abs(ring)*22)*exp(-age*1.3)*strength; uv += dir*w*.014; colour += w*.16`.

## Assembling smooth object (SDF → mesh → pieces)

1. Model as a signed distance field with smooth unions (`smin`), e.g. ellipsoid crown + sphere cusps + tapered roots.
2. Sample on a grid (coarse pass first; exact field only in a band near the surface), extract with surface nets; normals from the field gradient (4-tap tetrahedron); optional AO by sampling the field along the normal.
3. Cut into ~160 pieces (nearest of random surface seeds); per piece: centre, swirl angle, start distance/height, delay (bottom first), random spin axis.
4. Vertex shader: `k = clamp((t-delay)/.5,0,1); e = smootherstep(k); r = 1-e;` centre spirals in with `rotY(cen + away*dist*r + up*r, (swirl + t*1.1)*r)`; local spin `turn(pos-cen, axis, angle*r)`; appear `smoothstep(delay*.6, delay*.6+.14, t)` scales the piece from .25 and drives alpha.
5. Build in time slices; upload; warm up; only then start the hero text.

## Arabic / RTL motion

- Set `dir="rtl"` on `<html>`; use logical CSS (`margin-inline-start`, `inset-inline-end`).
- Horizontal scroll section: mirror the shift. LTR `translateX(-p * travel)` → RTL `translateX(p * travel)` with the track aligned to the right (`justify-content: flex-end` or `direction: rtl` on the track).
- Swipe decks: "next" is a swipe to the left in LTR and to the right in RTL; flip the sign of the fly direction and of the arrow buttons.
- Progress rails, marquees, journey paths and slide-ins start from the reading start (right in RTL).
- GSAP/ScrollTrigger and Lenis scroll vertically and need no change; any `x:` values you animate must be mirrored (multiply by `dir === 'rtl' ? -1 : 1`).
- Split Arabic text by word, never by letter (letter spans break joining).

## SVG path morph (same point count)

```js
// a and b: arrays of numbers taken from two paths drawn with the same commands and number of points
const lerpPath = (a, b, t) => a.map((v, i) => v + (b[i] - v) * t);
const toD = n => `M${n[0]} ${n[1]} C${n.slice(2).join(' ')}`; // match your path's command layout
path.setAttribute('d', toD(lerpPath(knot, straight, easeInOut(p))));
```
Different point counts: use GSAP MorphSVG or flubber.

## Travelling companion object

Fixed full-screen canvas (`pointer-events:none`). Keyframes `{ y: scrollY, pose: 'spotName', rx, ry, op }`. Each section has an invisible `.spot` box; cache its geometry (absolute top, or offset inside its sticky panel) on resize. Per frame: find the two keys around `scrollY`, smootherstep between them, read poses from cached numbers. Size = `min(spotH, spotW/0.8)`.
