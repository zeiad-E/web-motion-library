# Effects catalog

~100 web motion effects in 12 families. Columns:
**Effect** (other names) · **What the visitor sees** · **How it's built** · **Cost** (build effort / runtime weight: L, M, H) · **Impact** (what it does to the audience) · **Watch out**.

Effort assumes a developer comfortable with JS and the tools in `tools.md`. Runtime weight is about phones.

**Touch versions of cursor/hover effects** (phones have no hover):

| Desktop effect | Touch version |
|---|---|
| Magnetic button, tilt card, spotlight | Gentle idle motion, or react to tap/press; or device tilt (`deviceorientation`, ask permission on iOS) |
| Cursor-follow preview | Tap row expands an inline preview |
| X-ray lens, depth-map, layered parallax | Lens/scene wanders on its own; drag moves it |
| Hover glitch / image hover | Trigger when the element enters view |
| Custom cursor, sparkle trail | Drop, or attach to touch-move inside one section |

---

## 1. Scroll-driven (the scroll moves a story, not just the page)

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Image-sequence scrub (scrollytelling video, frame scrub) | A "video" that plays forward/back with the scroll: an object assembles, liquid pours, a product opens | 120–240 frames (WebP/AVIF) drawn to `<canvas>`; scroll progress → frame index; preload first 10, stream the rest | M/M | The most shared effect of 2024–26; feels like a film you control | Frame weight on phones; make a 900px set; draw in rAF, never in the scroll handler |
| Pinned storytelling (sticky scenes, scrollytelling beats) | One panel stays still while text beats change | Tall section (300–500vh) + `position: sticky` panel; progress 0→1 picks the beat | L/L | Focus: one idea at a time; great for "how it works" | Crossfade beats tightly or two overlap; give a progress rail |
| Horizontal scroll section | Vertical scroll slides a row of cards sideways | Sticky panel; `translateX = -p × (trackWidth − viewport)` | L/L | Gallery feeling, breaks the vertical rhythm | Card height must fit 100vh minus header |
| Curved ribbon / bending carousel | Cards sit on a curve that bends more when you scroll fast | Each card on a circle (CSS 3D), curvature tied to scroll velocity; WebGL version bends real images in a vertex shader | M/L–M | "Alive" gallery; reacts to how *you* scroll | Clamp curvature; fade far cards |
| Zoom-through portal (fly-through mask) | You fly through a shape (logo, keyhole, tooth) into the next scene | Full-screen color layer with a shape-shaped hole (SVG `<mask>`); hole scales exponentially with scroll; scene behind settles 1.25→1 | M/L | Cinematic chapter change; "step inside" | Exponential growth (`pow(k, p^1.4)`) or it opens too early |
| Scroll-drawn path (line drawing, journey map) | A path draws itself; a marker rides it; milestones light up; camera pans | SVG path: `stroke-dashoffset = len × (1−p)`, `getPointAtLength` for the marker | M/L | Explains a process or timeline without words | Pan the SVG so the marker stays centred |
| Exploded layers on scroll (layer split, architecture reveal) | A UI/device splits into stacked 3D layers, then a pulse travels through them | CSS 3D: rotateX/Z the stack, `translateZ(i × gap × open)`; a glowing dot moves between layer Z values | M/L | Shows "how it works / real tech" | Labels get hidden behind upper layers: put them left of the stack or on the layers |
| Scroll-reactive 3D object | A 3D object rotates/moves as you scroll | Three.js / raw WebGL; scroll → rotation, camera, shader uniforms | M–H/M | Premium, tactile | One signature 3D per page |
| Travelling companion object | The hero object leaves the hero and follows you through sections, turning to new angles | One fixed full-screen canvas; keyframes = (scrollY, screen spot, rotation, opacity); invisible "spot" divs in each section say where it stands | H/M | The thread that makes many sections feel like one story | Measure spots once (on resize), never read layout per frame; let it step aside for loud sections |
| Parallax layers (classic parallax) | Background, mid and foreground move at different speeds | translateY = scroll × depth per layer | L/L | Depth, calm luxury | Overused; keep factors small |
| Velocity marquee | Giant text rows drift, speed up, skew and blur with scroll speed | Duplicated text track; x += base + velocity; `skewX(velocity)` | L/L | Energy band between sections; brand promises | Can feel loud for serious brands |
| Scroll-linked counters (stat tickers) | Numbers count up as they enter view | IntersectionObserver + eased count; or tie to progress | L/L | Credibility ("12,000 patients") | Only real numbers |
| Reveal on scroll (fade/slide-up) | Blocks rise and fade in as they arrive | IO + class, or CSS `animation-timeline: view()` | L/L | Polished baseline | Too many = sluggish; stagger 60–90ms |
| Clip-path reveal (wipe reveal) | Images uncover with a moving mask edge | `clip-path: inset()` animated by progress | L/L | Editorial feel | — |
| Text highlight on scroll | Words light up one by one as you read | Split words; opacity/colour tied to progress | L/L | Forces reading pace on manifestos | Keep text short |
| Sticky stacking cards | Cards pile on top of each other as you scroll | Each card `position: sticky` with growing top offset + scale-down of previous | L/L | Shows a sequence of features/services | — |
| Scroll-snapped full-screen chapters | Each scroll moves exactly one scene | `scroll-snap-type` or GSAP snap | L/L | Presentation feel | Frustrating if scenes are long |
| Seamless loop | Reaching the footer flows back into the hero | Duplicate hero at the end, jump scroll silently | M/L | "Never ends" wow | Disorients; skip on content-heavy sites |
| Scroll progress bar / rail | A line fills as you move through a section or page | width/height = progress | L/L | Orientation in long pinned sections | — |

## 2. 3D & WebGL

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Assembling object (shard/vortex build) | Dozens of pieces spiral in and land to form the hero object | Mesh cut into pieces (Voronoi on the surface); vertex shader moves each piece from a start orbit to its place with per-piece delay + smootherstep | H/M | "Creation" moment; unforgettable first second | Pieces must appear staggered, not pop in; warm up the shader before animating text; time-slice mesh building |
| Particle morph | Thousands of dots flow from shape to shape (logo → icon → icon) | Sample target points from shapes drawn off-screen; dots ease/spring to targets; Three.js Points or canvas 2D | M/M | Pure "space/tech" feeling; explains skills as shapes | ~1.5k dots in canvas 2D, 50k+ in WebGL |
| Depth-map 2.5D (Lando-style) | A flat photo bends like 3D with the mouse | Image + grayscale depth map; fragment shader offsets uv by mouse × depth (two-step lookup reduces smear) | M/L | Premium portrait/hero for very little weight | Blur the depth map; AI tools make depth maps |
| Layered parallax scene (fake 3D) | Illustrated layers move at different speeds with the mouse | 4–8 DOM layers, translate by mouse × depth, slight rotateX/Y | L/L | Friendly, playful depth | Idle drift when no mouse (phones) |
| Liquid ripple / water distortion | The hero turns to liquid; cursor drags waves; click splashes | Fragment shader sums expanding rings; offsets uv + highlight on crests | M/M | Fresh, sensory (beverages, spas, cleaning) | Text baked into the texture is not accessible; keep real HTML |
| Glass / chrome / iridescent object | Shiny rings, jewels, glass blobs catching light | Three.js `MeshPhysicalMaterial` (transmission, clearcoat) + env map; or Spline | M/M–H | Luxury, product beauty | Transmission is GPU heavy on phones |
| Smooth sculpted object (SDF modelling) | An organic, soft object (tooth, pebble, mascot) with smooth light | Signed distance field with smooth unions → mesh (surface nets / marching cubes) or raymarching | H/M | Bespoke 3D without a 3D artist | Build the mesh in time slices; normals from the field |
| Floating image cubes / cards in space | Project photos on boxes drifting in dark space | Three.js boxes + textures + mouse parallax | M/M | Portfolio wow | Readability of images |
| Explorable world | You drive/walk through the site | Full Three.js game + physics (Rapier/Cannon) | VH/H | Legendary (Bruno Simon) | Months of work; always ship a plain fallback |
| Spline embed | A designed 3D scene reacting to scroll/hover | Spline export (runtime or iframe) | L–M/M–H | Fast 3D without code | Runtime weight; limited control |
| 3D model viewer (product turntable) | Drag to spin a product | `<model-viewer>` or Three.js + GLTF | L–M/M | E-commerce confidence | Compress GLTF (Draco/Meshopt) |
| Shader gradient / mesh gradient hero | A slowly flowing aurora of colour | Fragment shader noise (or CSS blurred blobs) | L–M/L | Calm premium backdrop (Stripe-like) | Banding; add grain |
| Noise/displacement hover on images | Images warp, ripple or pixelate on hover | WebGL plane per image, displacement texture | M/M | Gallery richness | Many WebGL contexts: share one canvas |
| 3D tilt card | Cards tilt toward the cursor with a moving light | rotateX/Y from pointer + radial-gradient highlight | L/L | Tactile, premium | Reduce angle on large cards |
| Globe / data globe | A spinning globe with arcs and points | three-globe / cobe / custom | M/M | Global reach, logistics, SaaS scale | Heavy textures |
| Click-to-advance 3D scenes | Each click plays the next segment (door opens, steps of a treatment) | Pre-rendered video segments or canvas states; play one segment per click | L/L | Calm, controlled explanation | Show progress dots and a restart |

## 3. Image & media

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Before/after slider (with spring) | Drag a handle to compare two images | Clip the "after" at handle %; spring physics; keyboard arrows | L/L | The most persuasive element for results-based businesses | Real photos, same framing |
| X-ray / flashlight reveal (spotlight mask) | The cursor becomes a lens that reveals a hidden layer | Two aligned layers; top one masked with `radial-gradient` at the cursor | L/L | Curiosity + "see what experts see" | Align geometry exactly; idle wander on phones |
| Hover glitch (RGB split) | Image splits into colour slices briefly | Cloned layers + stepped `clip-path` + hue shift | L/L | Tech/edgy | Wrong for trust-first brands |
| Pixel-block transition | Blocks sweep over content and reveal the next | Grid of divs scaling in with distance-based delay; swap at full cover | L/L | Playful transition, before/after | Avoid striped colour patterns (coprime counts) |
| Full-screen video case studies | Muted looping video instead of a text block | `<video autoplay muted playsinline loop>` lazy-loaded | L/M | "Show, don't tell" | Poster image; pause off-screen |
| Cursor-follow preview | Hover a list item → its image floats at the cursor | One absolutely positioned card, lerped to the pointer | L/L | Fast scanning + rich detail | Keyboard focus position; hide on touch |
| Image trail | Moving the cursor leaves a trail of images | Pool of images placed at pointer intervals, fading out | L/L | Playful portfolios | Distracting on content pages |
| Ken Burns / slow zoom | Photos slowly zoom/pan | CSS keyframes on transform | L/L | Cinematic calm | — |
| Duotone / colour-shift on hover | Photos switch from duotone to full colour | CSS filter or blend modes | L/L | Brand consistency in photo grids | — |
| Mask-text video (video inside letters) | A video plays inside giant letters | `background-clip: text` with video, or SVG mask | L/M | Bold hero | Legibility |
| Scroll-scrubbed video | A real `<video>` scrubbed by scroll | Set `currentTime` from progress | L/M | Simpler than frames | Choppy unless every frame is a keyframe; prefer image sequences |
| Lottie / Rive illustrations | Vector animations, interactive state machines (Rive) | Lottie JSON or Rive file | L/L | Friendly explanation, icons that react | Rive for interaction, Lottie for playback |

## 4. Typography

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Letter-by-letter reveal (split text) | Letters rise/fade in one by one | Split into spans; reveal each on a frame-timed step (not fixed CSS delays: they jump after a main-thread stall) | L/L | Premium first impression | 40–60ms per letter feels right |
| Rotating phrase (word cycler) | One line keeps swapping its last words | Spans with rise-in / rise-out; start cycling only after the hero settled | L/L | Many value props in one line | Keep phrases similar length |
| Text scramble / decode | Characters cycle through random glyphs, then settle | Per-letter settle frame; random glyph until then | L/L | "AI / system is writing" | Escape HTML; store the final text |
| Typewriter with caret | Text types with a blinking cursor | Timed slices + caret span | L/L | Terminal/AI vibe | Slow typing tests patience |
| Giant name hero / edge-cut type | The name fills the width, cut off at the edges | Container units (`cqw`), masked line rise | L/L | Confidence, identity | Wrap behaviour on phones |
| Outline-to-fill text | Outlined words fill with colour on scroll/hover | `-webkit-text-stroke` + background-clip progress | L/L | Editorial | — |
| Variable-font animation | Letters change weight/width with scroll or cursor | Animate `font-variation-settings` | L/L | Subtle, crafted | Needs a variable font |
| Kinetic typography / text on path | Words flow along curves or bounce to rhythm | SVG `<textPath>` or GSAP SplitText | M/L | Music, events, youth brands | Readability |
| ASCII / character art | An image drawn with characters that shimmer; cursor is a torch | Sample brightness on a tiny canvas → map to ramp ` .:-=+*#%@` | M/L | Developer/AI identity | Not for trust-first brands |
| SVG shape morph (path morph) | A line or icon smoothly turns into another shape (knot → straight line, icon → icon) | Interpolate between two paths with the same number/order of points (hand-made), or GSAP MorphSVG / flubber for different shapes; drive by time or scroll | L–M/L | Meaningful transformations in one small asset | Draw both shapes with matching anchor points |
| Handwritten scribbles | Circles, arrows and notes draw themselves | SVG `stroke-dashoffset`; handwriting font | L/L | Human warmth; points at the CTA | One or two per page |
| Parallax text lines with motion blur | Lines slide at different speeds; blur with speed | Per-line speed + CSS blur on velocity | M/L | Energy | — |
| Highlighted keywords (marker sweep) | A marker colour sweeps behind key words | `background-size` animation on a span | L/L | Guides skimmers to key facts | — |

## 5. Cursor & hover

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Magnetic button | The button leans toward the cursor and glows | Translate by offset × 0.3; glow from pointer vars | L/L | Makes the main CTA feel alive | Desktop only |
| Custom cursor (blend cursor) | A circle cursor that grows over links and inverts colour | Fixed div lerped to pointer, `mix-blend-mode: difference` | L/L | Signature feel | Keep the native cursor for text fields |
| Sparkle / particle cursor trail | Sparkles and a fading line follow the cursor | Small canvas of fading points | L/L | Playful (kids, beauty) | Only one section |
| Spotlight / torch hover | A soft light follows the cursor over a dark section | radial-gradient positioned by pointer vars | L/L | Mystery, exploration | — |
| Hover lift + shadow | Cards rise slightly with a deeper shadow | transform + box-shadow transition | L/L | Baseline polish | — |
| Interactive objects as navigation | Hanging keychains/tags that swing; click opens a section | 2D/3D objects with spring physics | M/L | Memorable personal idea | Accessible fallback nav |
| Gooey menu / metaball buttons | Options squeeze out like toothpaste and snap apart | SVG filter: blur + alpha contrast (`feColorMatrix`), icons on a separate crisp layer | M/L | Fun contact button | Filter cost on big areas |
| Drag & throw (physics) | Elements can be dragged and thrown, bounce off edges | Pointer capture + velocity + spring | M/L | Play, delight | Must not block scrolling on touch |
| Swipe deck (Tinder cards) | Flick cards away; the next rises | Pointer capture, threshold on distance/speed, cycle to back | M/L | Reviews/testimonials get read | Buttons for keyboard users |

## 6. Navigation & page transitions

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Colour-panel page wipe | Two colour panels sweep over, the page swaps underneath | Web Animations API; swap while covered | M/L | Agency-grade navigation | Keep under ~900ms |
| View Transitions (shared element morph) | A card morphs into the next page's hero | View Transitions API (`document.startViewTransition`, cross-document in modern browsers) | L–M/L | App-like continuity | Progressive enhancement |
| Menu reveal (full-screen menu) | The menu wipes/expands from the burger | clip-path circle growth or panels | L/L | Brand moment on phones | Focus trap |
| Loader → hero handoff | A preloader counter morphs into the hero | Counter + element that becomes the hero logo | M/L | Hides asset loading, sets the story | Under 2s, skip on return visits |
| Keyboard / arrow navigation between scenes | Arrow keys jump between sections | Key listeners → scroll targets | L/L | Power-user delight | Don't hijack keys in inputs |
| Sticky smart header | Header hides on scroll down, returns on scroll up | Track scroll direction | L/L | More room for content | — |

## 7. Loaders & entrances

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Boot loader / terminal intro | A terminal types system checks, a counter hits 100 | Timed lines + eased counter | L/L | Story in 2 seconds (tech, AI) | Patients/customers hate waiting: skip for service businesses |
| Choreographed hero entrance | Eyebrow → headline letters → phrase → paragraph → buttons, each waiting for the previous | Async sequence (await each step), started after heavy work (shader warm-up, mesh build) | L/L | Calm confidence; nothing jumps | Never drive it with fixed CSS delays |
| Skeleton shimmer | Placeholder blocks shimmer while data loads | CSS gradient animation | L/L | Perceived speed | — |
| Logo draw-on | The logo outline draws itself, then fills | SVG stroke-dashoffset then fill | L/L | Brand moment | — |

## 8. Layout & aesthetic motion

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Drifting mesh gradient + grain | Soft colour blobs drift, film grain on top | Blurred blobs with slow keyframes + SVG noise overlay | L/L | Premium calm | Big blur areas cost on low-end GPUs |
| Glassmorphism card with moving light | Frosted card tilts toward the cursor; light follows | backdrop-filter + pointer vars | L/M | Premium booking/confirmation cards | backdrop-filter over animated canvases re-blurs every frame |
| Anti-grid / scattered layout | Elements float freely, drift slightly | Absolute positions in a loose grid | L/L | Youth/Gen-Z energy | Overwhelming quickly |
| Bento grid with micro-motion | Grid of tiles each with a tiny looping animation | CSS/Lottie per tile | L–M/L | SaaS feature overviews | Pause off-screen |
| Infinite logo/review marquee | Logos or quotes glide endlessly | Duplicated track + CSS animation | L/L | Social proof | Pause on hover |
| Noise / dither overlays | Subtle animated grain | Small noise texture shifting | L/L | Analog warmth | — |

## 9. Micro-interactions & feedback

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Ripple / press feedback | Buttons ripple where pressed | Span scaled from the pointer | L/L | Confirms the tap | — |
| Success morph (button → check) | The button turns into a check after submit | Width/shape morph + SVG check draw | L/L | Satisfaction | Announce for screen readers |
| Live chat with typing dots | An assistant answers with dots, text streams in, cites a source, offers slots | Scripted demo or real SSE stream | M/L | Proves an AI product instantly | Keep it fast (two letters per tick) |
| Toggle with physics | Switches overshoot slightly | Spring CSS (`linear()` easing) | L/L | Craft | — |
| Form field focus glow / float labels | Labels float up, a glow follows focus | CSS | L/L | Forms feel easy | — |
| Confetti / celebration burst | Particles burst on success | canvas-confetti | L/L | Reward | Not for serious contexts |
| Number roll (odometer) | Prices/stats roll like an odometer | Stacked digits translateY | L/L | Playful precision | — |

## 10. Data & explanation motion

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| Animated chart build | Bars/lines draw in as they enter | SVG path draw / scale | L/L | Credibility for numbers | Start axes at zero |
| Step-by-step diagram | A process diagram lights each step with scroll | Pinned SVG + progress | M/L | Teaches a process | — |
| Comparison toggle | Toggle "without us / with us" morphs the scene | State morph (FLIP) | M/L | Clear value | — |
| Map route draw | A route draws across a map to the location | SVG path on a static map | L/L | Local businesses, logistics | — |

## 11. Sound & haptics (use sparingly)

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| UI sounds (opt-in) | Soft clicks/whooshes | WebAudio, off by default | L/L | Immersive portfolios/games | Always opt-in |
| Haptic tap (phones) | A tiny vibration on key actions | `navigator.vibrate` (Android) | L/L | Physical feedback | Not on iOS Safari |

## 12. AI-produced assets (the pipeline behind many effects)

| Effect | What the visitor sees | How it's built | Cost | Impact | Watch out |
|---|---|---|---|---|---|
| AI hero sequence | A photo-real object assembling/pouring/transforming | Image model (two stills: start/end) → video model (8–14s, static camera, no text) → ffmpeg frames → image sequence | M/M | Film quality for a small budget | Vague prompts produce generic results; write a prompt guide |
| Relit portrait | Your own face with cinematic rim light | Image model copies a reference's lighting | L/L | Personal, premium portfolio hero | Keep likeness |
| AI depth map | Depth map for any photo | Depth models (e.g. Depth Anything) | L/L | Enables depth-map 2.5D | Clean edges by hand |
| Image → 3D model | A rough 3D asset from one image | Tripo / similar → Blender cleanup | M/M | 3D without a 3D artist | Needs retopology/compression |
