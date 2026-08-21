# Terminal Portfolio - Aryaman Bhavya Gupta

An interactive terminal-themed personal portfolio website featuring a 3D graph visualization of professional experience, projects, and skills. Built for a Computer Engineering graduate specializing in cybersecurity and machine learning.

## What it accomplishes

**Professional Showcase**: Presents work experience at PricewaterhouseCoopers, academic projects, and technical skills in cybersecurity (IAM/SailPoint/Okta), machine learning, and full-stack development.

**Interactive Experience**: Features a Three.js-powered 3D graph that visualizes connections between projects, work experience, education, and technical skills - making portfolio exploration engaging and intuitive.

**Modern Web Interface**: Terminal aesthetic with Matrix-style animations, responsive design, and smooth page transitions that reflect technical expertise while maintaining professional presentation.

Built with vanilla HTML/CSS/JavaScript and Three.js - no frameworks, demonstrating core web development skills.

## Content source of truth

Review and edit portfolio facts and copy in
[`personal_website_content.md`](personal_website_content.md) first. That file
tracks the stable IDs used by the detailed pages, terminal readouts, globe
nodes, and preview cards, plus unresolved factual questions. The current
single-file website still duplicates copy in `index.html`, so approved content
must be synced there until the site is refactored to load structured data.

---

## Technical Documentation

### Architecture Overview

The website is a single-file application (`index.html`) with five main systems:

1. **Data Management System** - Portfolio content and graph structure
2. **3D Visualization System** - Three.js-based interactive graph
3. **Navigation System** - Page transitions and menu handling  
4. **Animation System** - Matrix effects, transitions, and UI feedback
5. **Responsive Layout System** - Multi-breakpoint design with touch optimization

### Core Data Structures

#### `graphData` Object (lines 1220-1297)
```javascript
{
  nodes: [
    { id: "about", name: "ABOUT", category: "core", x: 200, y: 150 }
  ],
  links: [
    { source: "about", target: "contact" }
  ]
}
```
**Critical Dependencies:** All functions that manipulate the 3D graph
**If Modified:** Update `sectionContent` keys to match node IDs

#### `sectionContent` Object (lines 1300-1350) 
```javascript
{
  about: "ABOUT_ME.exe loaded...\n\nContent here...",
  project1: "PROJECT.exe loaded...\n\nProject details..."
}
```
**Critical Dependencies:** `selectSection()`, `showContent()`, `showCategorySummary()`
**If Modified:** Keys must correspond to node IDs in `graphData`

### Function Dependencies & Inter-relationships

#### Initialization Chain
```
DOMContentLoaded Event
├── initGraph() ────────────── Creates 3D visualization
│   ├── Creates: scene, camera, renderer, nodeObjects[], linkObjects[]
│   ├── Calls: addGraphInteraction(), animate3D()
│   └── Dependencies: graphData, DOM element #graph
├── initMenu() ────────────── Sets up navigation
│   ├── Calls: navigateToPage(), selectSection(), highlightGraphNodes()
│   └── Dependencies: Menu items with data-section attributes
└── selectSection('about', 'core') ── Sets initial state
```

#### Navigation Flow
```
Menu Click → navigateToPage(pageId)
├── findGraphNode(sectionId) ──── Locates corresponding 3D node
├── startTransitionAnimation() ── Orchestrates complex animation sequence
│   ├── Phase 1: startCodeDissolution() (dissolves menu/avatar)
│   ├── Phase 2: [Graph rotation - inline code, no dedicated function]
│   ├── Phase 3: [Camera animation - inline code, no dedicated function]
│   ├── Phase 4: showPageWithAnimation() (displays new page)
│   └── Phase 5: cleanupTransitionEffects() (resets state)
└── Updates: Page visibility, animation states

Direct Section Access → selectSection(sectionId, category)
├── Updates menu highlighting and graph node opacity
├── Calls showContent() or showCategorySummary()
└── No page navigation - content displayed in bottom panel
```

#### Content Display Chain
```
selectSection(sectionId, category)
├── Updates menu active states
├── Highlights graph nodes by category
├── Calls showContent() OR showCategorySummary()
│   ├── showContent() ── Typewriter effect with activeTypewriterTimeout
│   └── showCategorySummary() ── Category overview text
└── Dependencies: sectionContent object, nodeObjects[], linkObjects[]
```

### Critical Global State

#### Three.js Objects
```javascript
let scene, camera, renderer, graph3D;  // Core Three.js objects
let nodeObjects = [], linkObjects = []; // Rendered graph elements
```
**Used by:** `initGraph()`, `animate3D()`, `addGraphInteraction()`, highlighting functions
**If Modified:** All graph rendering and interaction functions affected

#### Animation State
```javascript
let rotationSpeed = 0.002;           // Graph rotation speed
let selectedCategory = null;         // Current category filter
let activeTypewriterTimeout = null;  // Content animation control
let resizeTimeout;                   // Debounces window resize events
```

### Function Modification Impact Analysis

#### High-Impact Functions (Modify with extreme caution)

**`initGraph()` (lines 1359-1458)**
- **Purpose:** Initializes entire 3D visualization system
- **Affects:** All graph rendering, interactions, and animations
- **Dependencies:** `graphData`, Three.js library, DOM element `#graph`
- **If Modified:** Test all graph interactions, highlighting, and animations

**`graphData` Object (lines 1220-1297)**
- **Purpose:** Defines portfolio structure and relationships
- **Affects:** Node positioning, link connections, content mapping
- **Critical:** Node IDs must match `sectionContent` keys
- **If Modified:** Verify all node references in links array are valid

#### Medium-Impact Functions

**`selectSection()` (lines 1801-1857)**
- **Purpose:** Updates content and visual highlighting
- **Dependencies:** `sectionContent`, `nodeObjects[]`, `linkObjects[]`
- **If Modified:** Test content display and graph highlighting

**`startTransitionAnimation()` (lines 1504-1531)**
- **Purpose:** Coordinates complex page transitions
- **Uses:** Sequential setTimeout calls for animation phases
- **If Modified:** Test timing and animation sequence synchronization

#### Low-Impact Functions (Safer to modify)

**`showContent()` (lines 1860-1886)**
- **Purpose:** Displays content with typewriter effect
- **Self-contained:** Limited external dependencies
- **If Modified:** Only affects content display animation

**`goBack()` (lines 1710-1732)**
- **Purpose:** Returns from page view to main interface
- **Affects:** Page visibility, menu state, animation cleanup
- **Dependencies:** Page containers, menu items, graph state

**`resetGraphHighlight()` & `highlightGraphNodes()` (lines 1908-1967)**
- **Purpose:** Manage 3D graph visual highlighting on hover/selection
- **Dependencies:** `nodeObjects[]`, `linkObjects[]` arrays
- **If Modified:** Test menu hover effects and category filtering

### HTML Structure Requirements

#### Required Elements
```html
<div id="graph"></div>                    <!-- Three.js container -->
<div id="content-display"></div>          <!-- Content output -->
<div id="content-text"></div>             <!-- Typewriter target -->
<div id="matrix-overlay"></div>           <!-- Animation overlay -->
```

#### Menu Item Format
```html
<div class="menu-item" data-section="about" data-category="core">
  > ABOUT_ME
</div>
```
**Required Attributes:** `data-section` (matches node ID), `data-category`

#### Page Container Format
```html
<div class="page-container" id="projects-page">
  <!-- Page content -->
</div>
```
**Naming Convention:** `{section}-page` where section matches node ID

### CSS Animation Dependencies

#### Critical CSS Classes
- `.active` - Visible state for pages and content
- `.dissolving` - Code dissolution animation trigger  
- `.entering` - Page entrance animation
- `.typing` - Typewriter effect for page titles

#### Animation Keyframes
- `@keyframes matrixFall` - Matrix rain effect
- `@keyframes codeDissolve` - Code dissolution transition
- `@keyframes pageEnter` - Page entrance animation
- `@keyframes typewriter` - Typewriter text effect

### Responsive Design System

#### Breakpoint Architecture
```css
/* Desktop: Default 3-column grid layout */
.terminal-content { 
  grid-template-columns: 1fr 400px 1fr; 
}

/* Tablet: 769px-1024px - Compressed 3-column */
@media (min-width: 769px) and (max-width: 1024px) {
  grid-template-columns: 1fr 350px 1fr;
}

/* Mobile: ≤768px - Transforms to vertical flexbox */
@media (max-width: 768px) {
  display: flex; flex-direction: column;
  /* Order: Menu → Graph → Avatar */
}

/* Small Mobile: ≤480px - Minimal spacing & borders */
@media (max-width: 480px) {
  border: none; padding: 5px;
}
```

#### Layout Transformation Chain
```
Desktop Grid (3-column)
├── Left Panel: Menu system
├── Center: 3D graph visualization  
└── Right Panel: Avatar display

Mobile Flexbox (vertical stack)
├── Order 1: Menu (full-width, centered text)
├── Order 2: Graph (300px height, compressed)
└── Order 3: Avatar (200x250px, centered)
```

#### Touch Optimization
```css
/* Touch-friendly menu items */
@media (hover: none) and (pointer: coarse) {
  .menu-item { 
    padding: 15px 10px;    /* Larger touch targets */
    min-height: 44px;      /* iOS minimum */
    background: rgba(0, 212, 255, 0.05); /* Visible boundaries */
  }
}
```

**Dependencies:** Window resize handler debounces layout recalculations
**If Modified:** Test across all breakpoints and touch devices

### Event Handling & 3D Interaction System

#### Window Resize Handler (lines 1991-2007)
```javascript
let resizeTimeout;
window.addEventListener('resize', () => {
  clearTimeout(resizeTimeout);
  resizeTimeout = setTimeout(() => {
    // Recalculate Three.js canvas dimensions
    camera.aspect = width / height;
    renderer.setSize(width, height);
  }, 250); // 250ms debounce
});
```

#### 3D Graph Interaction Chain
```
Mouse/Touch Event → addGraphInteraction()
├── Raycaster System
│   ├── mouse.x/y = normalized coordinates (-1 to 1)
│   ├── raycaster.setFromCamera(mouse, camera)
│   └── intersectObjects(spheres) → collision detection
├── Click Events → onGraphClick()
│   ├── Finds intersected 3D node
│   ├── Calls selectSection(nodeId, category)
│   └── Updates content panel (no page navigation)
└── Hover Events → onGraphMouseMove()
    ├── Scales up hovered nodes (1.2x sphere, 1.3x glow)
    ├── Changes cursor to pointer
    └── Resets other nodes to normal scale
```

#### Raycaster Configuration
```javascript
const raycaster = new THREE.Raycaster();
const mouse = new THREE.Vector2();

// Critical: Convert screen coordinates to normalized device coordinates
mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
```

**Performance Impact:** Raycaster calculations run on every mouse move
**Dependencies:** `nodeObjects[]` array structure, Three.js scene graph

### Performance Considerations

#### Animation Loop
```javascript
function animate3D() {
  requestAnimationFrame(animate3D);  // 60fps loop
  graph3D.rotation.y += rotationSpeed; // Continuous rotation
  renderer.render(scene, camera);
}
```
**Impact:** Runs continuously - modify `rotationSpeed` to adjust performance

#### Memory Management
- `activeTypewriterTimeout` - Always cleared before new content
- Matrix overlay - Cleaned up after transitions
- Three.js objects - Reused rather than recreated

### Complete Function Inventory

#### Core System Functions (20 total)

**Initialization & Setup (4 functions)**
- `initGraph()` - Creates entire 3D visualization system
- `initMenu()` - Sets up navigation event listeners
- `addGraphInteraction()` - Configures mouse/touch events for 3D graph
- `animate3D()` - Main animation loop (runs continuously)

**Navigation & Page Management (6 functions)**  
- `navigateToPage(pageId)` - Initiates full page transitions
- `startTransitionAnimation()` - Orchestrates 5-phase animation sequence
- `showPageWithAnimation()` - Handles page display with white flash effect
- `goBack()` - Returns from page to main interface
- `findGraphNode(sectionId)` - Locates 3D node by ID
- `cleanupTransitionEffects()` - Resets animation states

**Content & Display (4 functions)**
- `selectSection(sectionId, category)` - Updates content and highlighting
- `showContent(content)` - Displays text with typewriter effect  
- `showCategorySummary(category, sectionId)` - Shows category overview
- `resetGraphHighlight()` - Resets all node visual states

**Animation & Effects (6 functions)**
- `startMatrixEffect()` - Creates matrix rain overlay
- `createMatrixColumn()` - Individual matrix column helper
- `startCodeDissolution()` - Dissolves menu/avatar with code overlay
- `createCodeOverlay()` - Code animation helper function
- `highlightGraphNodes()` - Menu hover highlighting effects
- `animateGraph()` - Temporarily increases rotation speed

#### Function Dependency Matrix

**High Interdependency (Modify carefully)**
```
initGraph() ←→ graphData, nodeObjects[], linkObjects[]
  ↓ affects ↓
selectSection() → showContent() → activeTypewriterTimeout
resetGraphHighlight() → highlightGraphNodes() → nodeObjects[]
```

**Medium Interdependency**
```  
navigateToPage() → findGraphNode() → startTransitionAnimation()
  ↓ chain calls ↓
startCodeDissolution() → showPageWithAnimation() → cleanupTransitionEffects()
```

**Low Interdependency (Independent)**
```
createMatrixColumn() ← startMatrixEffect() (self-contained)
createCodeOverlay() ← startCodeDissolution() (helper function)  
animateGraph() (temporarily modifies rotationSpeed only)
```

#### Event Handler Functions (Nested)
- `onGraphClick(event)` - Nested inside `addGraphInteraction()`
- `onGraphMouseMove(event)` - Nested inside `addGraphInteraction()`  
- Window resize handler - Anonymous function in initialization

#### Obsolete/Placeholder Functions
- `updateClock()` - Exists but documented as removed (no functionality)

**Critical Rule:** Before modifying any function, check its position in the dependency matrix to understand ripple effects.

### Firefly Field & Tethered Node Cards

Two systems layered onto the globe. Both live in `index.html`; the firefly field
is WebGL, the cards are plain DOM/SVG projected on top of the canvas.

#### Firefly field (`createAmbientParticles`, `updateFireflyBloom`)

Replaces the original 150 flat particles with a ~900-point `THREE.Points` field
driven by a custom `ShaderMaterial`: a dense layer hugging the globe surface plus
a sparse outer drift. Points idle dim cyan with a per-point twinkle; near the
cursor they swell and shift cyan -> amber -> white-hot.

```
pointer (NDC)
  -> bloomRaycaster against bloomSphere (radius GLOBE_RADIUS + 0.12)
  -> bloomWorld (world space)
  -> bloomTrail[0..N]  each lerping toward the one before it  (the wake)
  -> fireflies.worldToLocal(...)  -> uBloom[i] uniform
```

**The bloom point is tracked in world space, the fireflies live in the rotating
group.** Converting world -> local each frame is what makes the lit patch stay
under the cursor while the globe turns through it. Using `graph3D` local space
instead would glue the glow to the sphere and break the effect.

Tuning constants (top of the 3D section): `BLOOM_TAPS`, `BLOOM_LERP`,
`BLOOM_WEIGHT`, `BLOOM_RADIUS`. Shader uniforms: `uTime`, `uBloom[]`,
`uBloomStrength[]`, `uBloomRadius[]`, `uCool`, `uWarm`, `uHot`, `uDrift`.

**Node halos:** each node carries a dedicated `bloomHalo` mesh driven *only* by
cursor proximity (`obj.proximity`). This is deliberate — `highlightGraphNodes()`
and `resetGraphHighlight()` own node opacity and scale, so a separate mesh means
the two systems can never fight over the same property.

#### Tethered node cards (`initNodeCards`, `updateNodeCards`)

Glass panels tethered to their node by an SVG line, positioned by projecting the
node's world position to canvas pixels each frame.

- **Content:** `nodeCards[nodeId]` — optional `kicker`, `meter` (progress bar),
  `stats` (label/value rows), `tags` (chips), `link`. Rendered by
  `buildCardMarkup()`; all text passes through `escapeCardText()`.
- **Layout:** two regimes. The *hovered* node's card pops up directly beside it
  (`HOVER_OFFSET` gap, flips flank when out of room, sits above edge cards via
  `.is-hovered`). The ambient *featured* cards pin to the canvas **edges**
  (`CARD_EDGE`) — the graph column is only ~500px wide — and reach in with a
  long tether. Max `MAX_PER_SIDE` per edge with vertical collision resolution;
  a card whose preferred side is full falls back to the other, and edge cards
  dodge the hovered card when it intrudes into their band.
- **Visibility:** driven by `frontness` — the dot of the node's normal against
  the camera direction. Nodes rotating to the back fade out.
- **Disabled** below `CARDS_MIN_WIDTH` canvas width, and hidden by CSS under a
  900px viewport.

Cards are interactive once readable: past 50% opacity a card gains `.is-live`
(`pointer-events: auto`). Clicking a card acts like clicking its node (opens the
terminal readout), hovering lights it and holds the featured pair in place, and
a `link` entry renders as a real `<a>` opening in a new tab. During a drag the
layer gets `.is-dragging`, which force-disables card pointer events so a swipe
crossing a card keeps rotating the globe instead of dying at the card edge.

**Frame pacing:** every per-frame easing/decay constant (bloom strength, wake
lerps, card fades, rotation smoothing, momentum damping) is tuned against a
120Hz display (`REF_FPS`). `animate3D` computes `frameScale` — elapsed 120Hz
frames — each frame; accumulations multiply by it and decays exponentiate by it
(`easeK`), so the motion feels identical at any refresh rate. New per-frame
constants must go through `frameScale`/`easeK` or they will run refresh-rate
dependent.

#### Node rendering & camera framing

Each node is a layered "instrument contact", not a single sphere:

| Piece | What it is | Who drives it |
|---|---|---|
| `sphere` | small category-colored dot (r 0.085), also the userData carrier | `highlightGraphNodes` / `resetGraphHighlight` own its opacity + scale |
| `core` | white-hot kernel, child of `sphere` | opacity follows `sphere.material.opacity` each frame in `animate3D` (read-only) |
| `glow` | tight additive light bleed (r 0.13) | highlight functions own opacity + scale |
| `pulseRing` | thin reticle ring (r 0.15–0.165), billboarded to the camera every frame | `animate3D` owns opacity pulse + orientation |
| `bloomHalo` | amber cursor-proximity halo, `visible` only when lit | firefly bloom only (`obj.proximity`) |
| `hit` | invisible generous raycast target (r 0.19) | nothing — it exists so small dots stay easy to point at |

**r128 raycast quirks this depends on:** invisible meshes ARE still raycast
(that's why `hit` works), and `intersectObjects` recurses into children by
default (that's why `core.raycast` is stubbed out — otherwise hits would land
on the kernel and the `find(obj => obj.hit === ...)` lookup would miss).
All raycast call sites target `obj.hit`, never the visual meshes.

**Camera framing:** `fitCameraToGlobe(width, height)` replaces the old fixed
camera distances. It fits the globe (plus `R_EFF` margin for rings/fireflies)
to the *constraining* canvas axis — the old code keyed off `minDimension`,
which overflowed the portrait globe column horizontally. It also syncs the
firefly `uCamDist` uniform so point sizes survive any framing distance. Called
from `initGraph`, the debounced resize handler, and the post-layout settle
timeout — new camera-distance logic must go through it, not set `position.z`
directly.

#### Testing gotchas

- **Headless rendering depends on GPU flags.** With `--disable-gpu` (or under
  swiftshader) WebGL context creation fails and `initGraph` throws — nothing
  works, not even cards. Plain `--headless=new` on a Mac uses the real GPU and
  renders the globe fine, so `--screenshot` captures are trustworthy. Note that
  under `--virtual-time-budget` requestAnimationFrame barely pumps, so
  rAF-driven state (card fades, bloom) reads much earlier than wall-clock
  timers suggest — verify interactions with injected-script diagnostics, not
  pixels. macOS has no `timeout`; wrap Chrome with
  `perl -e 'alarm N; exec @ARGV' -- <chrome ...>`.
- **`--virtual-time-budget` never expires** against this page, because
  `animate3D` schedules `requestAnimationFrame` forever. Inject a head script
  that wraps `requestAnimationFrame` and stops scheduling after ~250 frames.
- Top-level `let`/`const` in the inline script are reachable from a second
  injected `<script>`, so a test harness can poke `pointerInside`, `pointerNDC`,
  `hoveredCardId` and `featuredIds` directly.

### Common Modification Scenarios

#### Adding New Portfolio Content
1. Add node to `graphData.nodes[]` with unique ID
2. Add corresponding content to `sectionContent` object  
3. Add links to connect new node to existing nodes
4. Optional: Create dedicated page container with `{id}-page` naming

#### Modifying Graph Appearance
1. Update `categoryColors` object in `initGraph()` for node colors
2. Modify node geometry/materials for different shapes
3. Adjust camera position and rotation speeds
4. Update link styling and opacity values

#### Customizing Animations
1. Modify CSS keyframes for different visual effects
2. Adjust `setTimeout` delays in `startTransitionAnimation()` for timing
3. Update `rotationSpeed` and animation parameters
4. Customize typewriter speed in `showContent()`

#### Responsive Layout Modifications
1. **Add New Breakpoint**: Insert media query between existing breakpoints
2. **Modify Grid Structure**: Update `grid-template-columns` ratios in `.terminal-content`
3. **Touch Target Sizing**: Adjust `min-height` and `padding` in touch-specific media queries
4. **Graph Dimensions**: Modify height constraints in mobile-specific `.graph-container`

#### 3D Interaction Tuning
1. **Node Hover Scaling**: Modify scale values in `onGraphMouseMove()` (default: 1.18x sphere, 1.28x glow — kept subtle because the popped card is the primary hover feedback)
2. **Raycaster Sensitivity**: Adjust the shared `hitGeometry` radius (default 0.19) — NOT the visible sphere geometry
3. **Globe Framing**: Adjust `R_EFF` / `FILL` inside `fitCameraToGlobe()`
4. **Performance**: Increase resize debounce timeout (default: 250ms) for slower devices

This architecture ensures maintainability through clear separation of concerns and well-defined dependencies between systems.
