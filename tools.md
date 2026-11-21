# Tools & stack

Pin exact versions in production; check current docs before using an API from memory.

## Choosing

| Need | Default choice | Alternatives / notes |
|---|---|---|
| Smooth inertial scroll | Lenis | ~20 lines of lerp on wheel (see recipes) for prototypes; never on reduced motion |
| Scroll-driven timelines, pinning, scrubbing | GSAP + ScrollTrigger | CSS `animation-timeline: scroll()/view()` for simple reveals (progressive enhancement); hand-written sticky + progress math |
| Text splitting | GSAP SplitText | Hand-split spans (keep `aria-label` on the parent; split Arabic by word) |
| Tweening UI in React | Motion (formerly Framer Motion) | GSAP with `useGSAP` |
| Page transitions | View Transitions API | Barba.js (multi-page), Web Animations API panels |
| 3D scenes | Three.js | React Three Fiber + drei in React apps; OGL for tiny bundles |
| A single shader effect (ripple, depth map, gradient) | Raw WebGL full-screen quad | Three.js `ShaderMaterial`; curtains-style libs for DOM-synced images |
| Particles | Three.js Points / GPGPU | Canvas 2D up to ~2k points |
| No-code 3D | Spline | Export runtime; watch weight |
| Vector animation | Rive (interactive state machines), Lottie (playback) | CSS/SVG for simple icons |
| 3D model viewing | `<model-viewer>` | Three.js GLTFLoader + Draco/Meshopt compression |
| Physics | Rapier, cannon-es, Matter.js (2D) | Hand-written springs for UI |
| Sound | Howler.js / WebAudio | Always opt-in |

## AI asset pipeline (for cinematic heroes)

1. **Stills**: image model makes a start frame and end frame (same framing; e.g. "object broken into floating parts" and "object assembled"), or relights a real photo.
2. **Motion**: video model animates start → end, 8–14 s, static camera, no text.
3. **Frames**: `ffmpeg -i clip.mp4 -vf "fps=15,scale=1600:-1" frames/%04d.webp` (+ a 900px set for phones). 120–180 frames is plenty.
4. **Depth maps**: depth model for depth-map 2.5D.
5. **3D**: image → 3D tools for rough models, Blender to clean, compress to GLB.
6. **Prompt guide, not one-liners**: write the page spec (sections, scroll lengths, beats, performance rules) before generating code or assets. Vague input gives generic output.

## Rough budgets (mid-range phone, 4G)

| Item | Budget |
|---|---|
| Hero first paint (first frame/still visible) | < 1.5 s |
| Hero image sequence | 150–180 frames × ~40–60 KB (900px WebP) ≈ 7–10 MB, streamed after the first 10 |
| Motion JS (GSAP + ScrollTrigger + Lenis) | ~60–80 KB gzipped |
| Three.js | ~150 KB gzipped; lazy-load near the 3D section |
| A raw WebGL shader effect | < 10 KB |
| Lottie file | < 100 KB each; Rive files are usually smaller |
| Per-frame JS work | < 4 ms (leave room for rendering inside 16.7 ms) |
| Any single main-thread task | < 50 ms (no "long tasks") |
| UI micro-animations | 150–400 ms; anxious audiences ≤ 600 ms |

## Testing tools

- Chrome DevTools Performance panel (long tasks, layout thrash), Rendering → "Paint flashing", "Frame rendering stats".
- Lighthouse (aim 90+ on mobile), WebPageTest on a real Android profile.
- Emulate `prefers-reduced-motion` in DevTools Rendering.
- A real mid-range Android phone before calling anything done.
