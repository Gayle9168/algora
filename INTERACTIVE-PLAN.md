# Algora — Interactive Build Plan (Phase 2)

You already have **Phase 1**: image-generation prompts (`marketing-remaining.md`, `authentication.md`,
`onboarding.md`, `learning-product.md`, `gamification.md`, `account.md`, `supporting-pages.md`,
`roadmap-builder.md`, `admin.md`) → static screenshots → a static Lovable site.

**Phase 2 is this document:** turning that static site into the best DSA-learning product in the
world, in ordered batches of copy-paste Lovable prompts.

**Companion docs:** `GROWTH.md` (why students arrive and how they share) and
`CURRICULUM-ORDER.md` (which 56 algorithms to build, in which order, and which to refuse).

---

## The one idea that makes Algora better than every competitor

Every DSA site is either **an animation toy** (VisuAlgo, sorting-visualizer clones) or **a problem
grinder** (LeetCode). Nobody owns the middle. Algora's moat is a single technical decision:

> **Algorithms are executed once, as pure functions, and emit an immutable array of `Step`s.
> The canvas, the code pane, the explanation pane, the complexity counter, and the narration are
> all *pure functions of `steps[i]`*.**

Consequences that competitors cannot copy quickly:
- Perfect sync between visual / code line / English sentence — free, forever, on every algorithm.
- Scrub backwards. Time-travel debugging of an algorithm is a genuinely new learning experience.
- "Run it on **your** input" and "run it on **your** code" reuse the exact same player.
- Auto-generated quizzes ("what does the queue look like at step 7?") from the step log.
- Auto-generated spaced-repetition cards from the same step log.
- Deterministic → snapshot-testable → 60+ algorithms without visual regressions.

Every batch below protects that architecture. **Batch 3 is the company.** Do not skip it.

---

## Batch map

| # | File | Batch | Why here |
|---|---|---|---|
| 1 | `interactive/batch-01-foundation.md` | Design system, tokens, fonts, shells, router, mock data | Everything else inherits this. Fix it once. |
| 2 | `interactive/batch-02-marketing-live.md` | Public pages become real: nav, hero live demo, pricing, forms, blog | Cheap wins, and it forces the player into existence early. |
| 3 | `interactive/batch-03-engine.md` | ★ The headless step-engine + playback store | The product. Pure TS, testable, no UI. |
| 4 | `interactive/batch-04-visualizer.md` | Renderers (array/tree/graph/grid/table) + 3-pane synced workspace | The flagship screen, now trivial because the engine exists. |
| 5 | `interactive/batch-05-explore-search.md` | Catalog, filters, live thumbnails, global search, path detail | Discovery layer over the algorithm registry. |
| 6 | `interactive/batch-06-lessons-review.md` | Lesson runner, inline quizzes, review queue + SM-2 scheduler | Turns visuals into retained knowledge. |
| 7 | `interactive/batch-07-practice-editor.md` | CodeMirror light editor, Web-Worker test runner, results screen | Practice loop. Sandboxed, no eval on the main thread. |
| 8 | `interactive/batch-08-gamification.md` | XP/level math, streaks, quests, mastery map, leagues, achievements | Retention. Built on one progress store. |
| 9 | `interactive/batch-09-onboarding-roadmap.md` | Goals → adaptive assessment → generated path, roadmap builder, account & analytics | Personalization; needs progress data to exist first. |
| 10 | `interactive/batch-10-backend-launch.md` | Supabase auth + schema + RLS, sync, admin console, hardening & launch | Last, so the client contract is already frozen. |
| 11 | `interactive/batch-11-seo-share.md` | Prerendering, per-route meta + JSON-LD, shareable step URLs, embeds, SEO content, funnel analytics | The only batch that brings *new* people. See below — run it early. |

Each batch file contains: **Goal → Prerequisites → numbered copy-paste prompts → Acceptance
checklist → Known failure modes & repair prompts.**

### Recommended real order (not numeric order)

```
01 → 02 → 03 → 04 → 05 → 11 → 06 → 07 → 08 → 09 → 10
```

> **Superseded once 01, 03 and 04 are done.** Batch 02 sat at position two only because it "forces
> the player into existence early" — the engine and visualizer now exist, so that reason is spent.
> The live order from here is in `interactive/ready/00-ORDER-AFTER-04.md`:
> `05 → 02 (minus auth) → 11 → 06 → 07 → 08 → 09 → 10 (+ the auth screens)`.
> That file also resolves the conflicting order printed in `ready/batch-03-PASTE-PACK.md`.

Batches 6–9 make people **stay**; batch 11 is what makes people **arrive**. A retention feature
with no traffic retains nobody, so once the visualizer and catalog are real (end of 05), ship the
growth layer before the deeper learning loop. Strategy and channel reasoning live in `GROWTH.md`.

---

## How to run a batch (repeat 10×)

1. Open `interactive/00-shared-context.md`, copy the **CONTEXT BLOCK**.
2. Open the batch file. For prompt 1: paste CONTEXT BLOCK, then the prompt body. Send.
3. Read the diff. If it touched files it shouldn't have, revert and re-send with
   `Only modify <exact paths>.`
4. Click through the app yourself. Test the acceptance line for that prompt.
5. Next prompt. Never batch two prompts into one message.
6. End of batch: run the Acceptance Checklist top to bottom. All green → snapshot/commit with the
   message `batch-0N: <name>`.
7. If a batch goes sideways, roll back to the last snapshot rather than patching forward.

## Rhythm that works

- 1 batch ≈ 1 focused session. Batches 3, 4 and 7 are the heavy ones — give each its own session.
- After batches 4, 7 and 8, do a "demo pass": record a 60-second screen capture. If it doesn't feel
  magic, fix feel before adding features.
- Do not start batch 10 (backend) until 1–9 are green. Auth bugs on top of UI bugs are 5× harder.

## Definition of done for Phase 2

- 43 student-facing routes reachable, no dead links, no placeholder screens.
- ≥ 12 algorithms fully visualized through the shared engine (BFS, DFS, Dijkstra, Topological
  Sort, Union-Find, Binary Search, Quicksort, Merge Sort, Heap Sort, Two Pointers, Sliding Window,
  0/1 Knapsack DP) with step-accurate code highlighting and narration.
- Scrub backwards works everywhere. Reduced-motion respected everywhere.
- Progress, XP, streak, and review scheduling survive a reload and a login on another device.
- Lighthouse ≥ 90 performance / 100 accessibility on `/` and `/algorithms/bfs`.
- Zero `any`, zero console errors, zero hardcoded hex outside the token file.

---

## Phase 3 (later — do not start early)

Content scale-up to 60+ algorithms via the registry; AI tutor that explains `steps[i]` in natural
language on demand; "explain my code" diff against the canonical step log; classroom/cohort
dashboards from `campus.md`; mobile app reusing `src/engine` unchanged.

(Shareable step permalinks moved *out* of Phase 3 — they are batch 11, prompt 11.3. They are an
acquisition mechanic, not a nice-to-have, so they ship with the growth layer.)
