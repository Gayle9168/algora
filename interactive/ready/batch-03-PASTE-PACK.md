# Batch 03 — PASTE PACK (Lovable)

Everything below is **copy-paste ready**. Paste one block per Lovable message, in order,
and verify before moving to the next. Do not merge two prompts into one message.

---

## Where you are

Batch 01 (foundation: tokens, fonts, shells, routing) is **done in Lovable**. Batch 02
(marketing pages) is deliberately **deferred to near the end** — its hero/section demos must
run on the real engine, so building it now means building it twice.

Next up is **batch 03, the engine**. Do STEP 0, then PROMPT 3.0 (which now doubles as a
batch-01 acceptance check), then 3.1 → 3.5.

Dependency-correct order for the whole project:

```
01 foundation  ✅ done
03 engine      ← you are here
04 visualizer
05 explore
06 lessons
07 practice
09 onboarding + roadmap
08 gamification
10 backend (Supabase)
02 marketing (now with live engine demos)
11 SEO + share images
```

---

## STEP 0 — One-time Lovable setup (do this before prompt 3.0)

Open your Lovable project → **Settings → Knowledge** (a.k.a. Project instructions) and paste
the CONTEXT BLOCK below. Lovable applies Knowledge to *every* message automatically, so you
stop re-pasting it and the design stops drifting at screen 30.

```
PROJECT: "algora" — a gamified platform where CS students master data structures & algorithms
through synchronized visualization, code, and plain-English explanation.
Tagline: "See the algorithm think."

STACK (do not change): Vite + React + TypeScript + Tailwind CSS + shadcn/ui + react-router-dom.
Animation: framer-motion for UI transitions; hand-written SVG (Canvas only where noted) for
algorithm visuals. State: zustand for engine/playback state, TanStack Query only when a real
backend exists. No Redux, no MUI, no Chakra, no styled-components, no D3 for layout.

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
border-hairline…). Never hardcode a hex value inside a component.

FOLDER LAYOUT (put files exactly here):
src/app | src/components/{ui,common,viz,player} | src/engine/{algorithms} | src/stores |
src/data | src/hooks | src/lib | src/pages | src/styles

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

Then **create a Lovable snapshot / commit to GitHub now**, before you touch anything. Every
green prompt below gets its own snapshot so you can roll back one prompt, not ten.

---

## PROMPT 3.0 — Read-only audit (no code changes)

> Why: your static site was generated with mock data. Before the engine lands we need to know
> exactly what fake shapes already exist, or Lovable will invent a second set of types and you
> will spend a week reconciling them.

```
AUDIT ONLY — do not create, modify or delete any file in this message.

Report the following about the current repo, as a short markdown answer:

1. Every file under src/data/** with its exported names and, for each export, the TypeScript
   shape of one element (2-3 lines max each).
2. Every file that currently renders an algorithm visual, animation, array of bars, graph,
   tree, grid, or a "play / pause / next step" control — full path plus one line on how it
   currently produces its animation (setTimeout? css keyframes? hardcoded array of states?).
3. Every place a step / frame / state-of-an-algorithm type is already declared, even loosely
   (e.g. type Cell = 'active' | 'visited'), with the file path.
4. Whether `zustand`, `vitest`, and `framer-motion` are already in package.json, and which
   Tailwind tokens from the design system are already wired in tailwind.config.ts
   (list the token names that exist, and the ones that are missing).
5. The list of routes currently registered in the router.
6. Batch-01 acceptance check: confirm (yes/no + file path) that
   (a) the light-theme tokens are defined once in a single source of truth,
   (b) Instrument Sans and JetBrains Mono are loaded and mapped to font-sans / font-mono,
   (c) no component file contains a hardcoded hex colour — if any do, list those files,
   (d) no dark-mode / dark: class or dark background remains anywhere.

Do not propose changes. Do not write code. Just the report.
```

**What to do with the answer:** paste it back to me. If item 2 lists components that fake
animation with timers, we mark them for deletion in batch 04 instead of trying to save them.
If item 6 finds hardcoded hexes or leftover `dark:` classes, we clean those in one small
follow-up prompt *before* 3.1 — token debt gets 10x more expensive once 40 more screens exist.

---

## PROMPT 3.1 — The contract: types + StepBuilder

> Why: this file is the product. Every visual, code highlight, narration line, counter, quiz
> and share-image later derives from `steps[i]`. Get it wrong and all 43 pages inherit the bug.

```
Create `src/engine/types.ts` — the permanent contract of the whole product. No implementation.

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
Do not create any component, page or route in this prompt.
```

**Accept when:** the app still builds, `src/engine/types.ts` + `builder.ts` exist, and nothing
else changed. If Lovable also "helpfully" built a visualizer, undo and re-run this prompt with
`Only create the two files named. Delete anything else you added.`

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
   DFS/Dijkstra later, so keep it generic and pure.
   Aux panels: the queue, and a visit-order log.
   Node states: frontier when enqueued, active when dequeued, visited when finished.
   Counters: enqueues, dequeues, edgesExamined.

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
`listModules()`. Registry keys must match the slugs already used in `src/data/algorithms.ts` —
read that file first and reuse its exact slugs. Do not edit src/data.

No React, no components, no routes in this prompt.
```

---

## PROMPT 3.3 — Player store (the only place playback lives)

```
Create `src/stores/playerStore.ts` with zustand. Import types from `src/engine/types.ts` and the
registry from `src/engine/registry.ts`. Do not modify src/engine — it is final.

State: slug: string | null, run: AlgorithmRun | null, index: number, isPlaying: boolean,
speed: 0.25|0.5|1|1.5|2|4, loop: boolean, error: string | null,
rawInputs: Record<string,string>.

Actions: load(slug, rawInputs?)  // registry lookup -> module.validate -> module.run;
  on invalid input set `error` and leave the previous run intact,
play, pause, toggle, next, prev, seek(i), first, last, setSpeed, toggleLoop, reset,
stepToNextMilestone, stepToPrevMilestone, stepToNextPhase.
All index changes must clamp to [0, steps.length - 1]. Never mutate `run`.

Derived selectors exported as hooks from the same file: useCurrentStep(), useProgressPercent(),
useCounters(), useCodeLine(), useCanStepForward(), useCanStepBack(),
usePhaseSegments()  // contiguous phase ranges: { phase, from, to }[] for the timeline bar.

Create `src/hooks/useAutoplay.ts`: a requestAnimationFrame loop with accumulated delta time
(base 900ms per step ÷ speed) — NOT setInterval, NOT setTimeout. It must pause on tab blur
(visibilitychange), stop at the last step or wrap when loop is true, clean up its frame on
unmount, and when `prefers-reduced-motion` is set it must not auto-advance unless the user
explicitly pressed play.

Create `src/hooks/usePlayerKeys.ts`: Space play/pause, ArrowRight/ArrowLeft step,
Shift+Arrow milestone jump, Home/End first/last, 1-4 set speed, R reset. It must ignore the
event when focus is inside an input, textarea, select or contenteditable, and must not
preventDefault on keys it does not handle.

No visual components in this prompt.
```

---

## PROMPT 3.4 — Engine self-test harness (ugly on purpose, keep forever)

```
Add a dev-only route `/dev/engine` — register it in the router but do NOT link it from any nav.
Deliberately unstyled/minimal; this is a regression harness, not a designed page.

It contains: a slug dropdown from listModules(), the raw input fields from module.inputs, a Run
button, Prev / Next / Play buttons, "step i of n", the current narration, the current counters,
and a <pre> dump of JSON.stringify(currentStep, null, 2). Wire everything through
`playerStore` and `useAutoplay` — no local animation state.

Also add `src/engine/__tests__/engine.test.ts` with vitest (add vitest to devDependencies and
a "test" script if missing) asserting:
- binarySearch on [1..15] with target 13 finds it, and every step's codeLine is within
  1..pseudocode.length
- bubbleSort's final frame values are sorted ascending and the swaps counter is > 0
- bfs visit order from A on "A-B,A-C,B-D,C-D" is exactly A,B,C,D
- for all three modules: steps.length > 3, every step has a non-empty narration, counters never
  decrease between consecutive steps, and mutating steps[0].frame does not affect steps[1]
- an invalid input returns { ok:false, error } from validate() instead of throwing
```

**Accept when:** `/dev/engine` lets you scrub all three algorithms and the JSON changes
coherently at every step, forwards *and backwards*. This is the moment your product stops
being a mock. Snapshot here.

---

## PROMPT 3.5 — Nine more algorithms (same pattern, zero new concepts)

> Only run this after 3.4 is green. It is deliberately repetitive — that repetition is the
> proof your contract was right.

```
Do NOT modify src/engine/types.ts, src/engine/builder.ts, the structure of
src/engine/registry.ts, or src/stores/playerStore.ts — they are final. Add nine modules
following the exact pattern of the existing three, then register them:

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

## Acceptance checklist — Batch 03 (all must be green before batch 04)

- [ ] `src/engine/**` contains no `import React`, no `setTimeout`, no `window`, no `document`.
- [ ] 12 modules registered; every `listModules()` slug exists in `src/data/algorithms.ts`.
- [ ] Every step: `1 <= codeLine <= pseudocode.length`; `narration` non-empty.
- [ ] Scrubbing to step 0 after reaching the end gives byte-identical frame JSON.
- [ ] `codeByLang` line counts are aligned across js/ts/py for the same codeLine.
- [ ] All vitest tests pass.
- [ ] Invalid input shows a friendly error instead of a crash or a blank screen.
- [ ] No page or component outside `/dev/engine` was changed by this batch.

## Repair prompts (paste verbatim, change nothing else)

| Symptom | Repair prompt |
|---|---|
| Animation works but can't go backwards | `Steps must be precomputed and immutable. Remove all timer-driven mutation from the algorithm modules; the store may only change the index.` |
| Frames share object references | `Deep-clone the frame inside StepBuilder.emit so mutating one step cannot affect another. Add a test proving it.` |
| Code highlight off by one | `codeByLang lines must be 1-based and index-aligned with pseudocode. Pad shorter languages with blank lines so the same codeLine points at the equivalent statement.` |
| Narration reads like a log | `Rewrite every narration string as one plain-English present-tense sentence a beginner would understand. No variable dumps, no "i++".` |
| React sneaks into the engine | `src/engine must be framework-free. Move any hook or component out to src/components or src/hooks and change nothing else.` |
| It restyled unrelated pages | `Revert every change outside the files I named. This prompt touches only: <list>.` |
| It redefined types | `Do not redefine Step/Frame/AlgorithmModule. Import them from src/engine/types.ts and delete your duplicate declarations.` |
