# Batch 04 — Visualizer (paste pack, adapted to the real repo)

Engine is green: 12 modules, 40/40 tests, `/dev/engine` scrubs. Snapshot that as `engine-green`
before your first paste here — every prompt below is reversible only if you have it.

## Corrections applied vs `batch-04-visualizer.md`

The source doc was written for Vite + react-router + `src/pages/`. Your repo is
**TanStack Router file routes** + Tailwind **v4** (`@theme inline` in `src/styles.css`). So:

| Source doc says | This pack says |
|---|---|
| `src/pages/AlgorithmDetail/` | `src/routes/algorithms.$slug.tsx` via `createFileRoute` |
| "must not import react-router" | must not import `@tanstack/react-router` |
| `replaceState` for deep links | TanStack `validateSearch` + `navigate({ search, replace: true })` |
| One giant prompt 4.2 (7 renderers) | Split into **4.2a** and **4.2b** — see note below |
| Prompt 4.5 rewrites `HeroDemo` | **Deferred to batch 02**, which is late in the order |

**Coordinate space — confirmed.** Your harness dump showed `x: 58, y: 10` and `x: 10, y: 36.67`
on a graph frame. That is **normalised 0–100**, not pixels. Every SVG renderer therefore uses
`viewBox="-8 -8 116 116"` (the negative inset reserves room for node radius, badges and weight
labels so nothing clips at the edges) with `preserveAspectRatio="xMidYMid meet"`. This is stated
explicitly in 4.2b — if you let Lovable guess, you rebuild both graph renderers.

**Why 4.2 is split.** Seven renderers in one message is where Lovable silently half-finishes:
you get three good components and four stubs, and you won't notice until batch 05. Two smaller
pastes cost nothing extra and each one is independently verifiable.

## Paste order

```
4.1   playback controls
4.2a  ArrayView + AuxPanels + FrameView   → verify on /dev/engine
4.2b  Tree / Graph / Grid / Table         → verify all 12 modules
4.3   /algorithms/$slug workspace         ← deletes the fake NodeState
4.4   /visualizer playground + compare mode
```

Prefix every prompt with your Knowledge block already in Lovable settings — do not re-paste it.

---

## PROMPT 4.1 — Playback controls

```
Build `src/components/player/` wired to the existing `src/stores/playerStore.ts`.
Do not modify the store, the engine, or prefsStore.

Use these exact existing APIs — do not invent new ones:
  playerStore actions: play, pause, toggle, next, prev, seek(i), first, last, toggleLoop, reset,
    stepToNextMilestone, stepToPrevMilestone, stepToNextPhase
  selectors: useCurrentStep, useProgressPercent, useCounters, useCodeLine,
    useCanStepForward, useCanStepBack, usePhaseSegments
  speed lives in prefsStore only: read prefsStore.playbackSpeed, write via
    prefsStore.setPlaybackSpeed. Do NOT add speed state to playerStore.

- PlaybackBar.tsx — one row, bg-card, border-hairline, rounded-lg, sticky at the bottom of the
  visualizer column: [First][PrevMilestone][Prev][Play/Pause 44px primary][Next][NextMilestone]
  [Last] · SpeedControl · StepCounter · loop toggle · reset. lucide icons, each with aria-label
  and a Tooltip naming its keyboard shortcut. Disable Prev/Next from useCanStepBack/Forward.
- StepScrubber.tsx — accessible slider over 0…steps.length-1. Track renders phase segments as
  coloured bands from usePhaseSegments() and milestone ticks. Dragging seeks live. Hover shows
  that step's narration truncated to 80 chars. Arrows step, PageUp/PageDown jump 10.
  role="slider", aria-valuetext = the current narration.
- SpeedControl.tsx — segmented mono control: 0.25x 0.5x 1x 1.5x 2x 4x.
- StepCounter.tsx — mono "step 14 / 87" plus the phase name as a Chip.
- CounterStrip.tsx — counters from step.counters, mono, tiny label under each number. Tween with
  framer-motion; snap instantly when prefsStore.reducedMotion is true.

Do NOT mount usePlayerKeys or useAutoplay in these components — the workspace mounts them once.
No new routes in this prompt.
```

**Accept when:** drop `<PlaybackBar/>` into `/dev/engine` temporarily and it drives the harness.
Phase bands must be visible on bfs (it has multiple phases).

---

## PROMPT 4.2a — Array renderer + aux panels + dispatcher

```
Build `src/components/viz/`. Every renderer is pure presentational:
`(props: { frame: XFrame; className?: string }) => JSX`.
Import frame types from `src/engine/types.ts` — never redefine them.
Colours come ONLY from --viz-* tokens (including the --viz-sorted / --viz-edge-tree /
--viz-edge-rejected tokens added earlier). No hex values.

HARD RULE for this folder: no file may import a zustand store, src/engine/registry.ts, or
@tanstack/react-router. Frames arrive as props. No timers, no useEffect-driven animation.

- ArrayView.tsx — responsive SVG. Picks its own mode: bars when every value is numeric AND
  length >= 12, otherwise labelled cells. Cell fill = the value's state token. `pointers` render
  as labelled arrows BELOW the cell with framer-motion `layout` so they glide when the index
  changes. `ranges` render as a bracket + label ABOVE the cells. For `swapPair`, use
  framer-motion layoutId keyed by the value's identity so the two bars physically travel past
  each other instead of recolouring.
- AuxPanels.tsx — renders step.aux[]: 'stack' as a vertical stack that pushes/pops with
  framer-motion and marks the top item; 'queue' as a horizontal list with front/back labels;
  'keyvalue' as a two-column mono table with highlighted rows; 'log' as a scroll-locked list
  auto-scrolled to the last line.
- FrameView.tsx — switch on frame.kind, dispatch to the right renderer. For kinds not built yet
  (tree/graph/grid/table) render a small bg-tint placeholder reading "renderer: {kind}" — do NOT
  throw, and do NOT stub the renderers themselves.

All renderers: transition duration 0.35 ease [0.22,1,0.36,1]; zero animation under
prefers-reduced-motion; wrap SVG in <svg role="img"> with an aria-label describing the current
state in plain words.
```

**Accept when:** `/dev/engine` renders bubbleSort, insertionSort, binarySearch and slidingWindow
visually. Graph modules show the placeholder, not a crash.

---

## PROMPT 4.2b — Tree, Graph, Grid, Table

```
Add to `src/components/viz/` the four remaining renderers and register them in FrameView.tsx,
replacing the placeholders. Same hard rules as before: pure props, --viz-* tokens only, no store,
no registry, no @tanstack/react-router.

COORDINATE SPACE — this is fixed, do not change it and do not re-scale:
node x/y from the engine are already NORMALISED 0–100. Every SVG here uses
viewBox="-8 -8 116 116" preserveAspectRatio="xMidYMid meet", width 100%, height auto, and no
fixed pixel width. The -8 inset reserves space for node radius, badges and weight labels so
nothing clips. Node circles r=5 in that space (~36px at typical panel width); scale font sizes in
the same units so they stay proportional.

- TreeView.tsx — circles at x/y, label centred in mono, optional `badge` as a small pill at the
  top-right of the node. Edges as straight lines coloured by edge state.
- GraphView.tsx — TreeView plus: arrowhead markers when frame.directed; edge `weight` on a small
  card-coloured rounded rect at the edge midpoint when frame.weighted; each node's `dist` in mono
  underneath it, rendered as ∞ when dist is null or Infinity; edge states tree/rejected/active
  coloured from the tokens.
  A node whose state is unvisited and dist is null must still render normally — disconnected
  graphs are legal input and must never crash.
- GridView.tsx — CSS grid of divs, NOT SVG, so 30x30 stays fast. Cell state via tokens. `path`
  drawn as an accent overlay using absolute positioning. Preserve aspect-ratio.
- TableView.tsx — DP matrix: sticky row/col labels in mono, cell states, `active` cells scale
  1.06 with a ring. Horizontally scrollable with a fade edge indicator.
```

**Accept when:** on `/dev/engine` you walk **all 12** modules with no crash — especially dijkstra
(weights + ∞), topologicalSort (directed arrows), quicksort (recursion) and a **disconnected
graph** `A-B, C-D` starting at A.

---

## PROMPT 4.3 — The flagship workspace

```
Build the route `src/routes/algorithms.$slug.tsx` with createFileRoute, following the exact
conventions of the neighbouring files in src/routes. Compose ONLY existing pieces: playerStore,
FrameView, AuxPanels, PlaybackBar, StepScrubber, CounterStrip. Do not modify src/engine.

DELETE the mock visualization in the existing `src/routes/algorithms.bfs.tsx`, including its local
`NodeState` type and its hardcoded frames, and remove that route file entirely — /algorithms/bfs
is now served by this dynamic route. Remove the fake types first, then any imports left unused.
Do not touch the `Skill.state` type in mastery-map.tsx; that models progress, not algorithm state.

Header: breadcrumb Explore / Category / Name · h1 name · DifficultyBadge · category Chip ·
ComplexityTag row (best/avg/worst/space) · estMinutes + xp · actions: "Start lesson"
(/lessons/$slug), "Practice" (/practice/$slug), bookmark toggle, share button that copies the URL
including current input and step. Reuse the existing badge/chip components from batch 01 — do not
create new variants.

Body on lg+: grid-cols-[minmax(0,1fr)_380px], visual on the LEFT.
- LEFT: FrameView in a bg-card panel with a min-height so it never jumps between steps, then
  AuxPanels, CounterStrip, StepScrubber, and PlaybackBar pinned at the bottom.
- RIGHT, tabbed with mono tab labels:
  · Code — JS/TS/Py switcher persisted via prefsStore, line numbers, the current codeLine
    highlighted with bg-tint plus an accent left bar, auto-scrolled into view centred (smooth
    unless reducedMotion), copy button. Light theme — never a dark editor.
  · Explain — current narration in t-h3, `detail` below, and the previous two narrations greyed
    above for context.
  · Input — module.inputs rendered as real controls, preset buttons, "Run" calling
    load(slug, values), "Randomize", and the store's `error` shown inline in an error-tint box.
  · About — summary, real-world uses, common mistakes, prerequisites linked to other algorithms,
    and "when NOT to use this".

Under lg: stack vertically, visual first, sticky PlaybackBar above the tab strip.

Mount usePlayerKeys() and useAutoplay() exactly once here.

On mount call load(slug) with the module's first preset. If the slug has no engine module, render
an EmptyState "Visualization coming soon" plus the About tab — never crash.

Deep links via TanStack Router, NOT history.replaceState: declare validateSearch on the route for
{ input?: string; step?: number } where input is base64 of rawInputs. Read them with the route's
useSearch to restore state on mount, and write them with navigate({ search, replace: true }),
debounced ~300ms, as the user changes input or seeks.
```

**Accept when:** `/algorithms/bfs` → press Space → graph, queue, code line and sentence all move
together; drag the scrubber back to 0 and everything rewinds exactly.
Then `/algorithms/dijkstra?step=12` opens on step 12 with a correct distance table.

---

## PROMPT 4.4 — `/visualizer` playground + compare mode

```
Build `src/routes/visualizer.tsx` with createFileRoute, reusing the EXACT components from
algorithms.$slug.tsx. Do not duplicate any renderer or control.

Left rail 240px: algorithm picker grouped by category, listing only slugs that have an engine
module (from listModules()), with a search field to filter. Main area: the same workspace.

Compare mode: refactor `src/stores/playerStore.ts` into a `createPlayerStore()` factory and
provide instances through React context, KEEPING the existing default instance and all current
selector hooks working so algorithms.$slug.tsx and /dev/engine need no changes. Then a "Compare"
toggle splits the main area into two independent players running two algorithms on the SAME
input, with an option to share one scrubber, and a summary line like
"quicksort: 41 comparisons · bubble sort: 132".

Free-tier gate: after 3 different algorithms in one session show a soft, dismissible inline card
"Create a free account to unlock all 12 + save progress". Never a hard modal wall.
```

**Accept when:** `/dev/engine` and `/algorithms/bfs` still work unchanged after the factory
refactor. That is the whole risk of this prompt — verify it before snapshotting.

---

## Deferred out of this batch

**Prompt 4.5 (HeroDemo → real engine)** moves to batch 02. The marketing pages are late in your
build order precisely so their demos can run on the finished engine; doing it now means touching
HeroDemo twice.

## Batch 04 acceptance checklist

- [ ] All 12 modules render with no renderer crash (walk them in `/visualizer`)
- [ ] Visual, code line, narration, counters all identical after scrubbing back to step 0
- [ ] Code pane auto-scroll keeps the active line visible in a 400px pane
- [ ] `/algorithms/dijkstra?step=12` deep link correct
- [ ] Disconnected graph `A-B, C-D` from A completes; C and D never visited, no crash
- [ ] Panel height never jumps between steps
- [ ] Keyboard-only end to end; focus never trapped
- [ ] reduced-motion: usable, nothing animates
- [ ] No file in `src/components/viz` imports a store, the registry, or @tanstack/react-router
- [ ] `src/routes/algorithms.bfs.tsx` and its `NodeState` type are gone
- [ ] 30x30 grid holds ~50fps while playing
- [ ] `npm run test` still 40/40

## Repair prompts

| Symptom | Paste this |
|---|---|
| Renderer reads the store | `Make every component in src/components/viz purely presentational: the frame comes in as a prop. Remove all store imports.` |
| Layout jumps each step | `Give the visualization panel a fixed min-height and reserve space for pointers and labels so content cannot reflow between steps.` |
| Code pane scroll fights the user | `Only auto-scroll when the active line is outside the visible area, and cancel auto-scroll for 2s after any manual scroll.` |
| Bars recolour instead of moving | `Use framer-motion layoutId keyed by the value's identity so swapped elements animate along a path.` |
| Graph clips at the edges | `Use viewBox="-8 -8 116 116" preserveAspectRatio="xMidYMid meet" and remove all fixed pixel widths.` |
| Compare mode shares one store | `Give each player its own createPlayerStore() instance via React context; the default instance must keep working.` |
| Speed control desyncs | `Delete any playbackSpeed state in playerStore. Read and write it only through prefsStore.` |
