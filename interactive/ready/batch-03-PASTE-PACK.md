# Batch 03 — PASTE PACK (Lovable)

Revised against the real repo audit. Everything below is **copy-paste ready**. One block per
Lovable message, in order. Never merge two prompts into one message.

---

## What the audit changed

Your repo is **not** what the original `.md` files assumed. Four corrections now baked in below:

| Assumed in the old docs | Reality in your repo | Consequence |
|---|---|---|
| `react-router-dom`, `src/pages` | **TanStack Router**, file-based `src/routes/*.tsx` + `__root.tsx` | Knowledge block + prompt 3.4 route registration rewritten |
| `tailwind.config.ts` | Tailwind v4, `@theme inline` in `src/styles.css` | never tell Lovable to edit a tailwind config — it doesn't exist |
| tokens complete | missing `viz-sorted`, `viz-edge-tree`, `viz-edge-rejected`, `success`, duration scale | new PROMPT 3.0b patches them *before* the engine |
| playback state unowned | `src/stores/prefsStore.ts` already owns `playbackSpeed`, `narrationOn`, `reducedMotion` | playerStore must **read** prefs, not duplicate speed |

Two conflicts to resolve later, noted now so they don't ambush you:

- `src/routes/algorithms.bfs.tsx:48` declares `NodeState = "current"|"visited"|"unvisited"`.
  That is a **competing type system**. It gets deleted in batch 04, not migrated. Same for
  `mastery-map.tsx` `Skill.state` (that one is legitimately different — it's progress, not
  algorithm state — so it stays).
- `VizKind` in `src/data/types.ts` lists `linked-list | stack | queue`, but those are **aux
  panels**, not main frames, in the engine contract. Batch 04 maps them; don't "fix" either file.

Your 3 hardcoded-hex exceptions (Google `G` in `login.tsx`/`auth.tsx`, medal tiers in
`leagues.tsx`) are **correct and approved** — brand marks and metal gradients must not be
themeable. Leave them.

---

## Where you are

```
01 foundation  ✅ done (audit passed: single token source, fonts mapped, zero dark: classes)
03 engine      ← you are here
04 visualizer  (also deletes the fake NodeState visuals found in the audit)
05 explore → 06 lessons → 07 practice → 09 onboarding/roadmap → 08 gamification
10 backend (Supabase) → 02 marketing (live engine demos) → 11 SEO + share images
```

Batch 02 stays deferred: its hero demo must run on the real engine or you build it twice.

---

## STEP 0 — One-time Lovable setup

Lovable → **Settings → Knowledge** (Project instructions). Paste this. It applies to every
message automatically, which is what stops design drift at screen 30.

```
PROJECT: "algora" — a gamified platform where CS students master data structures & algorithms
through synchronized visualization, code, and plain-English explanation.
Tagline: "See the algorithm think."

STACK (do not change): Vite + React + TypeScript + Tailwind CSS v4 + shadcn/ui +
TanStack Router (file-based routes in src/routes). Animation: framer-motion for UI transitions;
hand-written SVG for algorithm visuals. State: zustand. No Redux, no MUI, no Chakra, no
styled-components, no D3 for layout, no react-router-dom.

THERE IS NO tailwind.config.ts. Tailwind v4 tokens live in `@theme inline` in src/styles.css.
Never create a tailwind config file.

DESIGN SYSTEM — LIGHT THEME ONLY (strict):
- paper background #F7F9F8, cards pure white #FFFFFF, hairline borders #E4E9E7
- ink text #0E1513 (headings/body), slate #5B6763 (secondary)
- ONE accent: teal #0E9C86, highlight #14B8A6, tint #E6F5F2
- semantic-only extras: amber #B4791A (warning), rose #C0453E (error) — never decorative
- Fonts: "Instrument Sans" for UI/headings, "JetBrains Mono" for code, stats, chips, labels
- radius 12–16px, 1px borders, pale-teal focus ring, generous whitespace, WCAG-AA contrast
- FORBIDDEN: dark backgrounds or black panels (the code editor is LIGHT too), purple/violet,
  rainbow gradients, glowing blobs/orbs, glassmorphism, neon, emojis as icons, stock photos of
  people, lorem ipsum. Flat solid colors, crisp edges, soft realistic shadows only.
- Icons: lucide-react, 1.5px stroke, sizes 16/20/24 only.

ALWAYS use Tailwind theme tokens (bg-paper, bg-card, text-ink, text-slate, text-accent,
border-hairline, bg-viz-active…). Never hardcode a hex value inside a component. The only
approved exceptions already in the repo are brand marks (Google G) and medal tier gradients.

FOLDER LAYOUT (put files exactly here):
src/routes (TanStack file routes) | src/components/{ui,common,viz,player} |
src/engine/{algorithms} | src/stores | src/data | src/hooks | src/lib | src/styles.css

RULES OF ENGAGEMENT for every prompt:
- Change ONLY what the prompt asks for. Do not refactor, rename, restyle or "improve"
  unrelated files. Do not touch design tokens unless told to.
- Reuse existing components before creating new ones. Search the repo first.
- Every new file is TypeScript with explicit prop types. No `any`.
- Keyboard + screen-reader accessible: real <button>, aria-labels, visible focus rings,
  respect `prefers-reduced-motion` on every animation.
- Mobile-first: must not break at 375px width.
- At the end of your reply, list the files you created/modified and nothing else.
```

Then **snapshot / commit to GitHub now**. Every green prompt below gets its own snapshot so you
can roll back one prompt, not ten.

---

## PROMPT 3.0b — Token + dependency patch (small, do it first)

> Why now: the engine declares a `sorted` cell state and `tree`/`rejected` edge states that have
> **no token yet**. If you skip this, batch 04 will invent a hex value for them and your single
> source of truth is dead. Cheap now, expensive after 40 screens.

```
Two small changes only. Do not touch any component, route or data file.

1. In src/styles.css, extend the existing token set (keep every current token exactly as is,
   same naming convention, light theme only):
   - --viz-sorted + --viz-sorted-ink   (a settled/confirmed state: calmer and clearly distinct
     from viz-found; a pale teal-neutral, must pass WCAG AA for its -ink pair)
   - --viz-edge-tree      (an edge accepted into the traversal tree — accent-strength)
   - --viz-edge-rejected  (an edge examined and discarded — must read as muted, NOT as error)
   - --success + --success-tint   (semantic only, to pair with the existing warning/error)
   - a duration scale: --duration-fast 120ms, --duration-base 220ms, --duration-slow 380ms
   Expose all of them in the @theme inline block so bg-viz-sorted, text-viz-sorted-ink,
   stroke-viz-edge-tree, stroke-viz-edge-rejected, text-success, bg-success-tint and
   duration-fast/base/slow work as Tailwind utilities.

2. Add `vitest` to devDependencies with a "test" script, and a minimal vitest config that
   resolves the same path aliases as vite.config. Do not add @testing-library or jsdom — the
   engine tests are pure Node, no DOM.

Do not restyle anything. Do not add dark variants.
```

**Accept when:** app still builds, `src/styles.css` grew by ~8 tokens, `npm run test` runs (0
tests is fine), nothing else changed.

---

## PROMPT 3.1 — The contract: types + StepBuilder

> Why: this file *is* the product. Every visual, code highlight, narration line, counter, quiz
> and share-image later derives from `steps[i]`. Get it wrong and all 28+ routes inherit the bug.

```
Create `src/engine/types.ts` — the permanent contract of the whole product. No implementation.

Note: `src/data/types.ts` already exists and is UNRELATED (content metadata). Do not modify it,
do not merge into it, do not re-export from it. The engine keeps its own types.

Frames describe what to draw; a Step bundles a frame with teaching metadata.

export type CellState = 'idle'|'active'|'visited'|'frontier'|'found'|'excluded'|'compare'|'sorted';
export type EdgeState = 'idle'|'active'|'tree'|'rejected';

export type ArrayFrame = { kind:'array'; values:(number|string)[];
  states: Record<number, CellState>;            // index -> state
  pointers: Array<{ name:string; index:number; color?:'accent'|'warning'|'error' }>;
  ranges: Array<{ from:number; to:number; label?:string; tone?:'tint'|'warning' }>;
  swapPair?: [number, number]; }

export type TreeFrame = { kind:'tree';
  nodes: Array<{ id:string; label:string|number; x:number; y:number; state:CellState;
                 badge?:string }>;
  edges: Array<{ from:string; to:string; state:EdgeState; label?:string }>; }

export type GraphFrame = { kind:'graph'; directed:boolean; weighted:boolean;
  nodes: Array<{ id:string; label:string; x:number; y:number; state:CellState;
                 dist?:number|null; badge?:string }>;
  edges: Array<{ from:string; to:string; weight?:number; state:EdgeState }>; }

export type GridFrame = { kind:'grid'; rows:number; cols:number;
  cells: Array<{ r:number; c:number; state:CellState; label?:string|number }>;
  path?: Array<[number,number]>; }

export type TableFrame = { kind:'table'; title?:string;
  rowLabels:(string|number)[]; colLabels:(string|number)[];
  cells: Array<{ r:number; c:number; value:string|number|null; state:CellState }>; }

export type Frame = ArrayFrame|TreeFrame|GraphFrame|GridFrame|TableFrame;

export type AuxPanel =
  | { kind:'stack'; label:string; items:Array<{id:string;label:string;state?:CellState}> }
  | { kind:'queue'; label:string; items:Array<{id:string;label:string;state?:CellState}> }
  | { kind:'keyvalue'; label:string; rows:Array<{k:string;v:string;highlight?:boolean}> }
  | { kind:'log'; label:string; lines:string[] };

export type Step = {
  i: number;                     // index in the step list, filled by the builder
  frame: Frame;
  aux?: AuxPanel[];              // stack / queue / dist table / log beside the main view
  codeLine: number;              // 1-based line in the algorithm's pseudocode
  narration: string;             // ONE plain-English sentence, present tense
  detail?: string;               // optional deeper 1-2 sentences
  phase: string;                 // e.g. 'partition', 'relax-edges' — used for the timeline
  counters: Record<string, number>;  // comparisons, swaps, visits, pushes...
  isMilestone?: boolean;         // scrubber tick marks + quiz anchor points
};

export type AlgorithmRun = {
  slug:string; steps: Step[]; pseudocode: string[];
  codeByLang: Record<'js'|'ts'|'py', string[]>;   // lines aligned to the SAME codeLine numbers
  inputSummary: string; result: string;           // human-readable outcome
  totalCounters: Record<string, number>;
};

export type InputField =
  | { name:string; label:string; kind:'numbers'; default:string; help?:string; max?:number }
  | { name:string; label:string; kind:'number'; default:number; min:number; max:number }
  | { name:string; label:string; kind:'text'; default:string }
  | { name:string; label:string; kind:'select'; default:string; options:string[] }
  | { name:string; label:string; kind:'graph'; default:string; help?:string }
  | { name:string; label:string; kind:'grid'; default:string; help?:string };

export type AlgorithmModule = {
  slug:string;
  inputs: InputField[];
  validate(raw:Record<string,string>): { ok:true; parsed:Record<string,unknown> }
                                     | { ok:false; error:string };
  run(parsed:Record<string,unknown>): AlgorithmRun;
  presets: Array<{ label:string; values:Record<string,string> }>;
};

Also create `src/engine/builder.ts` exporting a `StepBuilder` class used by every algorithm:
  new StepBuilder(pseudocode: string[], codeByLang: AlgorithmRun['codeByLang'])
  .emit({ frame, aux?, codeLine, narration, detail?, phase, isMilestone? })
  .bump(name: string, by = 1)   // mutates the running counter; applies to SUBSEQUENT steps
  .finish(slug, inputSummary, result): AlgorithmRun

StepBuilder requirements:
- emit() DEEP-CLONES frame and aux (structuredClone) so a later mutation cannot alter an
  earlier step, assigns `i` automatically, and snapshots the current counters onto the step.
- counters carry forward cumulatively; they never decrease.
- emit() throws a descriptive Error if codeLine < 1 or > pseudocode.length.
- finish() computes totalCounters from the last step and returns a frozen AlgorithmRun.

Pure TypeScript only. No React, no DOM, no window, no setTimeout anywhere in src/engine.
Do not create any component, route or visual in this prompt.
```

**Accept when:** app still builds, the two files exist, nothing else changed. If Lovable also
"helpfully" built a visualizer, undo and re-run with
`Only create the two files named. Delete anything else you added.`

---

## PROMPT 3.1b — Lock the builder with tests (do before 3.2)

> Why now: you have vitest installed with 0 tests, and 12 algorithms are about to be built on
> top of `StepBuilder`. The deep-clone and cumulative-counter rules are exactly the kind of bug
> that looks fine for months and then shows up as "scrubbing backwards shows the wrong board".
> Catching it now costs one prompt; catching it in batch 07 costs a rewrite of every algorithm.

```
Create `src/engine/builder.test.ts` using vitest. Test src/engine/builder.ts only.
Do not modify builder.ts or types.ts unless a test proves a real bug — if it does, fix the
source, never weaken the test.

Cover exactly these behaviours:
1. `i` is assigned sequentially from 0.
2. Deep-clone isolation: emit a step with an ArrayFrame, then MUTATE that same frame object
   (change values[0], add a pointer) and emit again. Assert step 0's frame is completely
   unchanged. This is the single most important test in the file.
3. Counters snapshot per step: bump('comparisons') between emits and assert each step holds the
   value as of ITS emit, and that a later bump does not retroactively change an earlier step.
4. Counters are cumulative and never decrease across the step list.
5. emit() throws a descriptive Error for codeLine 0, for codeLine = pseudocode.length + 1, and
   for a negative codeLine. Assert the message names the offending line number.
6. finish() returns totalCounters equal to the final step's counters, and the returned run is
   frozen (mutating run.steps or run.slug throws or is a no-op).
7. aux panels are deep-cloned too: mutate a queue's items array after emitting and assert the
   earlier step is unaffected.

Pure Node, no DOM, no React. Only create this one file.
```

**Accept when:** `npm run test` passes with ~7 green tests. If test 2 or 7 FAILS, that is a real
bug in the builder and you must fix `builder.ts` before running 3.2 — do not proceed.

---

## PROMPT 3.2 — First three algorithms (prove the contract)

```
Implement three AlgorithmModules using ONLY the existing StepBuilder and types from
`src/engine/types.ts`. Do NOT modify types.ts or builder.ts.

1. `src/engine/algorithms/binarySearch.ts` — inputs: sorted numbers list + target.
   Auto-sort the input and say so in inputSummary. Steps must show lo/hi/mid pointers, the
   excluded half greying out each iteration, and `found` or the exhausted range at the end.
   Counters: comparisons, iterations. Milestone on every mid computation.
2. `src/engine/algorithms/bubbleSort.ts` — inputs: numbers list.
   Show the compare pair, swapPair when swapping, and the growing `sorted` suffix.
   Counters: comparisons, swaps, passes.
3. `src/engine/algorithms/bfs.ts` — inputs: graph as an edge-list text field
   ("A-B, A-C, B-D", optionally weighted "A-B:4") + start node.
   Add `src/engine/layout.ts` with a BFS-layer auto-layout placing nodes in a 0-100 x / 0-100 y
   coordinate space, plus a circular fallback for disconnected nodes. It will be reused by
   DFS/Dijkstra/topologicalSort later, so keep it generic and pure.
   Aux panels: the queue, and a visit-order log.
   Node states: frontier when enqueued, active when dequeued, visited when finished.
   Edge states: tree for edges that discover a new node, rejected for edges to already-seen
   nodes. Counters: enqueues, dequeues, edgesExamined.

For each module:
- `pseudocode` is 8-16 numbered lines of clean pseudocode.
- `codeByLang` gives real, runnable JS, TS and Python whose line numbers correspond to the SAME
  codeLine values (pad with blank lines so alignment holds across all three languages).
- Narration is ONE present-tense sentence per step, written for a confused 20-year-old, e.g.
  "Middle value 12 is smaller than 30, so everything to the left can be thrown away."
  No variable dumps, no "i++".
- `presets`: 3 per algorithm, including one adversarial/worst case.
- `validate` rejects empty input, non-numeric tokens, arrays longer than 40, and unknown start
  nodes, returning a human-readable error string (not an exception).

Register them in `src/engine/registry.ts` exporting `getModule(slug)`, `hasModule(slug)`,
`listModules()`. Registry keys MUST match the exact slugs already in `src/data/algorithms.ts`
(24 algorithms) — read that file first and reuse its slugs verbatim. Do not edit src/data.

No React, no components, no routes in this prompt.
```

---

## PROMPT 3.3 — Player store (the only place playback lives)

```
Create `src/stores/playerStore.ts` with zustand. Import types from `src/engine/types.ts` and the
registry from `src/engine/registry.ts`. Do not modify src/engine — it is final.

IMPORTANT: `src/stores/prefsStore.ts` already exists and owns `playbackSpeed`, `narrationOn` and
`reducedMotion`. Those are USER PREFERENCES and stay there. playerStore must READ playbackSpeed
from prefsStore and must NOT declare its own speed state or its own reducedMotion. Do not
duplicate, do not migrate, do not delete prefsStore.

State: slug: string | null, run: AlgorithmRun | null, index: number, isPlaying: boolean,
loop: boolean, error: string | null, rawInputs: Record<string,string>.

Actions: load(slug, rawInputs?)  // registry lookup -> module.validate -> module.run;
  on invalid input set `error` and leave the previous run intact,
play, pause, toggle, next, prev, seek(i), first, last, toggleLoop, reset,
stepToNextMilestone, stepToPrevMilestone, stepToNextPhase.
Speed changes go through prefsStore.setPlaybackSpeed, not through this store.
All index changes must clamp to [0, steps.length - 1]. Never mutate `run`.

Derived selectors exported as hooks from the same file: useCurrentStep(), useProgressPercent(),
useCounters(), useCodeLine(), useCanStepForward(), useCanStepBack(),
usePhaseSegments()  // contiguous phase ranges: { phase, from, to }[] for the timeline bar.

Create `src/hooks/useAutoplay.ts`: a requestAnimationFrame loop with accumulated delta time
(base 900ms per step ÷ prefsStore.playbackSpeed) — NOT setInterval, NOT setTimeout. It must
pause on tab blur (visibilitychange), stop at the last step or wrap when loop is true, clean up
its frame on unmount, and when prefsStore.reducedMotion is true it must not auto-advance unless
the user explicitly pressed play.

Create `src/hooks/usePlayerKeys.ts`: Space play/pause, ArrowRight/ArrowLeft step,
Shift+Arrow milestone jump, Home/End first/last, 1-4 set speed (via prefsStore), R reset. It
must ignore the event when focus is inside an input, textarea, select or contenteditable, and
must not preventDefault on keys it does not handle.

No visual components in this prompt.
```

---

## PROMPT 3.4 — Engine self-test harness (ugly on purpose, keep forever)

```
Add a dev-only TanStack Router file route at `src/routes/dev.engine.tsx` (path /dev/engine).
Follow the existing file-route conventions in src/routes — use createFileRoute exactly like the
neighbouring route files do. Do NOT link it from any nav, header or footer.
Deliberately unstyled/minimal; this is a regression harness, not a designed page.

It contains: a slug dropdown from listModules(), the raw input fields from module.inputs, a Run
button, Prev / Next / Play buttons, "step i of n", the current narration, the current counters,
and a <pre> dump of JSON.stringify(currentStep, null, 2). Wire everything through
`playerStore` + `useAutoplay` — no local animation state, no setTimeout.

Also add `src/engine/__tests__/engine.test.ts` with vitest (already installed in 3.0b) asserting:
- binarySearch on [1..15] with target 13 finds it, and every step's codeLine is within
  1..pseudocode.length
- bubbleSort's final frame values are sorted ascending and the swaps counter is > 0
- bfs visit order from A on "A-B,A-C,B-D,C-D" is exactly A,B,C,D
- for all three modules: steps.length > 3, every step has a non-empty narration, counters never
  decrease between consecutive steps, and mutating steps[0].frame does not affect steps[1]
- an invalid input returns { ok:false, error } from validate() instead of throwing

Do not modify any existing route file other than adding the new one.
```

**Accept when:** `/dev/engine` scrubs all three algorithms and the JSON changes coherently at
every step, **forwards and backwards**. This is the moment your product stops being a mock.
Snapshot here.

---

## PROMPT 3.5 — Nine more algorithms (same pattern, zero new concepts)

> Only run after 3.4 is green. It is deliberately repetitive — that repetition is the proof your
> contract was right. If a module needs a new field on `Step`, the contract was wrong; stop and
> tell me instead of editing types.ts.

```
Do NOT modify src/engine/types.ts, src/engine/builder.ts, the structure of
src/engine/registry.ts, or src/stores/playerStore.ts — they are final. Add nine modules
following the exact pattern of the existing three, then register them (slugs must match
src/data/algorithms.ts verbatim):

1. insertionSort, 2. selectionSort — array frames, sorted-prefix highlighting
3. mergeSort   — array frame with `ranges` showing sub-arrays merging; aux 'log' panel of
                 merges; phases 'split' / 'merge'
4. quicksort   — pivot as a pointer, partition ranges, phases 'choose-pivot'/'partition'/'recurse'
5. heapSort    — TreeFrame for the heap PLUS the backing array in an aux keyvalue panel
6. dfs         — graph frame, aux 'stack' panel, edge states tree/rejected, phases enter/backtrack
7. dijkstra    — graph frame with `dist` per node, aux keyvalue distance table + priority queue
                 panel, edge relaxation highlighted, phase 'relax-edges'
8. topologicalSort — graph frame with in-degree badges, aux queue, phase 'peel'
9. slidingWindow   — array frame with a moving `ranges` window, aux keyvalue of window sum/max

Each needs: 8-16 pseudocode lines, aligned js/ts/py code, one plain-English present-tense
sentence of narration per step, 3 presets including a worst case, strict validate() returning
error strings, and accurate counters. Reuse src/engine/layout.ts for every graph algorithm.
Add one vitest per algorithm asserting a known-correct result.
```

---

## Acceptance checklist — Batch 03 (all green before batch 04)

- [ ] `src/engine/**` contains no `import React`, no `setTimeout`, no `window`, no `document`.
- [ ] 12 modules registered; every `listModules()` slug exists in `src/data/algorithms.ts`.
- [ ] Every step: `1 <= codeLine <= pseudocode.length`; `narration` non-empty.
- [ ] Scrubbing to step 0 after reaching the end gives byte-identical frame JSON.
- [ ] `codeByLang` line counts are aligned across js/ts/py for the same codeLine.
- [ ] `npm run test` passes.
- [ ] Invalid input shows a friendly error instead of a crash or a blank screen.
- [ ] `prefsStore.ts` still owns playbackSpeed; playerStore has no speed state of its own.
- [ ] No route or component outside `src/routes/dev.engine.tsx` was changed by this batch.
- [ ] `src/routes/algorithms.bfs.tsx` is **untouched** — its fake `NodeState` dies in batch 04.

## Repair prompts (paste verbatim, change nothing else)

| Symptom | Repair prompt |
|---|---|
| Animation works but can't go backwards | `Steps must be precomputed and immutable. Remove all timer-driven mutation from the algorithm modules; the store may only change the index.` |
| Frames share object references | `Deep-clone the frame inside StepBuilder.emit so mutating one step cannot affect another. Add a test proving it.` |
| Code highlight off by one | `codeByLang lines must be 1-based and index-aligned with pseudocode. Pad shorter languages with blank lines so the same codeLine points at the equivalent statement.` |
| Narration reads like a log | `Rewrite every narration string as one plain-English present-tense sentence a beginner would understand. No variable dumps, no "i++".` |
| React sneaks into the engine | `src/engine must be framework-free. Remove every React/DOM import from src/engine and move that logic into the store or a hook.` |
| Lovable duplicates playback speed | `playbackSpeed lives only in prefsStore. Delete the speed state from playerStore and read prefsStore instead.` |
| Lovable creates a tailwind config | `Delete tailwind.config.ts. This project is Tailwind v4 — tokens live in @theme inline in src/styles.css.` |
| Lovable installs react-router-dom | `Remove react-router-dom. Routing is TanStack Router with file routes in src/routes; follow the existing createFileRoute pattern.` |
