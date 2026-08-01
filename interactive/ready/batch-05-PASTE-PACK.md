# Batch 05 — Discovery (paste pack, adapted to the real repo)

Visualizer is green: 7 renderers, `/algorithms/$slug` and `/visualizer` drive the real engine.
Snapshot as `visualizer-green` before the first paste below.

**Read `00-ORDER-AFTER-04.md` first** — it explains why this batch runs before 02, and it contains
the DESIGN LOCK paragraph you must add to Lovable Knowledge before you paste anything here.

## Corrections applied vs `interactive/batch-05-explore-search.md`

| Source doc says | This pack says |
|---|---|
| `src/pages/Dashboard/`, `src/pages/Explore/` | the routes already exist as coded static pages in `src/routes` — open them and wire them, never recreate them |
| `useSearchParams` for filter state | TanStack `validateSearch` + `useSearch` + `navigate({ search, replace: true })` |
| "a static FrameView thumbnail" inside the card | a separate `src/components/common/AlgorithmThumbnail.tsx` — `src/components/viz/**` is forbidden from importing the registry (batch 04 rule), so the card cannot build its own frame |
| Explore's "Recommended" sort and the dashboard's ContinueCard each describe the same scoring | one `src/lib/recommend.ts`, built in 5.2, consumed by both |
| "algorithms mastered (n/24)" | read `algorithms.length` — never hardcode 24 |
| search index over "algorithms, lessons, problems, paths, posts, help articles" | `posts.ts` does not exist yet (batch 02 creates it). The index takes registered sources, so 02 adds posts without editing the scorer |
| one prompt for the progress store | 5.1 store + **5.1b tests** — the streak date logic and the XP curve are pure functions and they are the two things in this batch that silently corrupt persisted user data |

**24 algorithms, 12 engine modules.** Every card, thumbnail and CTA in this batch must call
`hasModule(slug)` and degrade gracefully. If you skip this, `/explore` crashes on the twelve
metadata-only algorithms and you will blame the renderers.

**Prompt order**

```
5.1   progressStore + src/lib/xp.ts
5.1b  vitest for xp + streak            → npm run test
5.2   AlgorithmThumbnail + src/lib/recommend.ts   (shared: explore, dashboard, and batch 02)
5.3   /explore
5.4   /app dashboard
5.5   src/lib/search.ts + Cmd-K palette + /search
5.6   /paths and /paths/$slug
```

One prompt per Lovable message. Never merge two. Your Knowledge block is already in Lovable
settings — do not re-paste it.

---

## PROMPT 5.1 — Progress store (the single source of learning state)

```
Create `src/stores/progressStore.ts` with zustand + persist (key 'algora-progress', version 1,
with a migrate function that returns the default state for any unknown version rather than
throwing). This is the ONLY place learning progress lives; later batches read it and none of them
may create a second progress store.

Do not modify src/engine — it is final. Do not touch src/stores/prefsStore.ts: playbackSpeed,
narrationOn and reducedMotion are USER PREFERENCES and stay there. Do not touch playerStore.
No UI, no routes, no restyling in this prompt.

State:
  xp: number; level: number;
  streak: { current:number; longest:number; lastActiveISO:string|null; freezesLeft:number };
  algorithms: Record<string, { status:'locked'|'new'|'watched'|'learning'|'practiced'|'mastered';
    stepsWatched:number; lessonDone:boolean; quizScore:number|null; problemsSolved:string[];
    lastSeenISO:string; masteryPct:number }>;
  lessons: Record<string, { completedAt:string|null; sectionIndex:number; quizScore:number|null }>;
  problems: Record<string, { attempts:number; solvedAt:string|null; bestRuntimeMs:number|null;
    lastCode:Record<'js'|'ts'|'py',string> }>;
  reviewCards: Record<string, { ease:number; intervalDays:number; dueISO:string; reps:number;
    lapses:number }>;
  quests: Record<string, { progress:number; claimedAt:string|null; periodKey:string }>;
  achievements: Record<string, { unlockedAt:string|null; progress:number }>;
  activity: Record<string, { xp:number; minutes:number; steps:number; solved:number }>;  // 'YYYY-MM-DD'
  bookmarks: string[];
  activePathSlug: string | null;

Actions: awardXp(amount, reason), recordStepsWatched(slug, n), markLessonSection(slug, i),
completeLesson(slug, quizScore), recordAttempt(problemSlug, code, lang),
markSolved(problemSlug, runtimeMs), touchStreak(), useFreeze(), gradeCard(cardId, grade 0-3),
setQuestProgress(id, n), claimQuest(id), toggleBookmark(slug), setActivePath(slug|null),
resetAll().

Also create `src/lib/xp.ts` — pure functions, no store import, no React:
  xpForLevel(level) = Math.round(100 * Math.pow(level, 1.35))   // xp needed to LEAVE this level
  levelFromXp(xp), xpToNextLevel(xp), progressPct(xp)
  Level 1 starts at 0 xp. levelFromXp must be monotonic and must never return 0 or NaN,
  including for xp = 0 and for absurd values like 1e9.

Rules:
- awardXp recomputes level via levelFromXp and RETURNS { leveledUp: boolean; newLevel: number }.
- touchStreak compares lastActiveISO to today in the user's LOCAL timezone using calendar days,
  not 24-hour arithmetic (a 23:50 → 00:10 session is two days; 08:00 → 23:00 is one).
  same day = no-op · exactly yesterday = current + 1 · older = reset to 1 unless freezesLeft > 0
  and useFreeze() covers the single missed day. longest = max(longest, current) always.
- masteryPct is DERIVED, never stored by hand: watched 20% + lesson 30% + (quizScore/100)*20% +
  min(problemsSolved.length/3,1)*30%, clamped 0-100, rounded.
- every mutation also upserts today's row in `activity`.
- seed from `src/data/user.ts` on first run only (persist rehydration must not re-seed), so the
  app never looks empty in a demo. If a slug in user.ts is not in src/data/algorithms.ts, skip it
  silently — do not invent entries.
- unknown slugs passed to any action are ignored, never crash, never create phantom records.

Also `src/hooks/useProgress.ts` exporting selector hooks only (no state): useAlgorithmProgress(slug),
useOverallMastery(), useCategoryMastery(), useDueCardCount(), useTodayActivity().
Read the real category values out of src/data/algorithms.ts — do not hardcode a category list.
```

**Accept when:** app builds, `/algorithms/bfs` still works, `localStorage['algora-progress']`
appears after any interaction, and nothing in `src/engine`, `prefsStore` or any route changed.

---

## PROMPT 5.1b — Lock the XP curve and the streak clock

```
Create `src/lib/xp.test.ts` and `src/stores/progressStore.test.ts` with vitest (already installed).
Test only those two modules. If a test proves a real bug, fix the source — never weaken the test.
Pure Node, no DOM, no React, no components.

xp.test.ts:
1. levelFromXp(0) === 1, and levelFromXp is monotonic non-decreasing across 0…200000 sampled.
2. levelFromXp(xpForLevel(n)) === n + 1 for n in 1…40  (crossing the threshold levels you up).
3. xpToNextLevel is always > 0, and progressPct stays within 0…100 for the same range.
4. No NaN or Infinity for 0, 1, 1e9.

progressStore.test.ts — drive the store directly, resetAll() in beforeEach, and FAKE THE CLOCK
with vi.setSystemTime so the streak tests are deterministic in any timezone:
5. touchStreak twice on the same calendar day leaves current unchanged.
6. touchStreak at 23:50 then at 00:10 the next calendar day increments current (this is the test
   that catches 24-hour arithmetic).
7. Skipping two days resets current to 1 and leaves longest at its previous peak.
8. Skipping one day with freezesLeft > 0 plus useFreeze() preserves the streak and decrements
   freezesLeft; with freezesLeft === 0 it resets.
9. awardXp returns leveledUp true exactly on the step that crosses a threshold, false otherwise.
10. masteryPct for an algorithm with watched + lesson + quizScore 100 + 3 problems === 100, and
    for a fresh algorithm === 0.
11. Every action writes today's activity row; xp in that row accumulates across calls.
12. Actions called with a slug that is not in src/data/algorithms.ts do not create a record.
```

**Accept when:** `npm run test` is green including the earlier 40 engine tests. A failure on 6, 8
or 12 is a real bug — fix it now. Persisted streak bugs are unfixable later; the wrong number is
already in every user's localStorage.

---

## PROMPT 5.2 — Shared card thumbnail + one recommendation engine

```
Two shared pieces that /explore, /app and (later) the landing page all consume. Building them now
is what stops three pages inventing three versions.

DESIGN LOCK: this prompt creates new components only. Do not open or modify any existing route.

1. `src/components/common/AlgorithmThumbnail.tsx`
   Props: { slug: string; animateOnHover?: boolean; className?: string }.
   It may import the registry (getModule/hasModule) — src/components/viz/** may NOT, which is why
   this file lives in common/ and not in viz/.
   - hasModule(slug) false → render a small bg-tint panel with the algorithm's category icon and
     no animation. Never crash, never blank.
   - true → lazily compute the run ONCE with the module's first preset, memoised in a module-level
     Map keyed by slug so ten cards for the same slug cost one run, and so remounts are free.
     Compute only when the card has been visible (IntersectionObserver), never at import time.
   - render step 0's frame through the existing <FrameView /> — do not write a new renderer, do not
     import a store.
   - animateOnHover: on hover OR keyboard focus, advance the local step index with a single
     requestAnimationFrame ticker at 2x (≈450ms/step) for 6 steps, then stop and hold. On leave/blur
     reset to step 0. NOT setInterval.
   - a module-level counter caps CONCURRENT animating thumbnails at 3; a 4th hover renders static.
   - prefersReducedMotion (read prefsStore.reducedMotion) → always static step 0, no ticker.
   - fixed aspect ratio and a min-height so a grid of cards never reflows while runs resolve.
   - aria-hidden on the visual; the parent card owns the accessible name.

2. `src/lib/recommend.ts` — pure functions, no React, no JSX:
   scoreAlgorithm(slug, progress, algorithms) and sortRecommended(slugs, progress)
     ordering: prerequisites satisfied first, then weakest category mastery, then lower difficulty,
     then shorter estMinutes, then alphabetical as a final deterministic tiebreak.
   nextBestAction(progress, data): { kind:'lesson'|'review'|'path'|'algorithm'; slug:string;
     reason:string; href:string } | null
     priority exactly: unfinished lesson > due review cards > next item in activePathSlug >
     next algorithm in the weakest category. `reason` is one human sentence, e.g.
     "you left off at section 3 of 6". Returns null only for a brand-new user with zero data.
   Prefer slugs where hasModule(slug) is true when the score ties, so a recommendation always has
   something to show.
   Read prerequisites/difficulty/estMinutes from src/data/algorithms.ts as they actually exist in
   that file — read it first, do not assume field names, do not edit src/data.

Also add `src/lib/recommend.test.ts`: sortRecommended is deterministic for identical input,
never drops or duplicates a slug, and nextBestAction returns each of the four kinds for four
hand-built progress fixtures.
```

**Accept when:** `npm run test` green, and dropping `<AlgorithmThumbnail slug="bfs" animateOnHover />`
into `/dev/engine` temporarily animates on hover and holds. Try `slug="kmp"` (or any
metadata-only slug) — it must render the fallback, not crash. Remove the temporary mount.

---

## PROMPT 5.3 — `/explore`

```
Wire the EXISTING `/explore` route in src/routes (open the file first — it is already coded as a
static page). Data: src/data/algorithms.ts + progressStore. Reuse AlgorithmThumbnail and
sortRecommended from prompt 5.2 — do not reimplement either.

DESIGN LOCK: keep the existing layout, spacing and className strings. You are adding state,
filtering, URL sync and real data — not redesigning. If the static page already has a toolbar or
card markup, wire THAT markup instead of writing new markup beside it.

Toolbar behaviour: search input over name + tags + category (debounced 200ms, fuzzy on name),
category chips (multi-select, each with a live count), difficulty segmented control, status filter
(All / Not started / In progress / Mastered, derived from progressStore status), sort
(Recommended / A-Z / Difficulty / Shortest), grid|list toggle.

URL STATE — TanStack Router, not react-router and not history.pushState:
declare validateSearch on the route for
{ q?: string; category?: string[]; difficulty?: string; status?: string; sort?: string; view?: 'grid'|'list' },
initialise every control from useSearch, and write changes with
navigate({ search: next, replace: true }) debounced ~300ms. Unknown or malformed values fall back
to the default instead of throwing. A full reload must restore the exact same result set.

Card: name, oneLiner, DifficultyBadge, category chip, ComplexityTag (average time), estMinutes,
mastery RingProgress from progressStore, bookmark toggle calling toggleBookmark, and
<AlgorithmThumbnail slug animateOnHover />. Whole card is one link to /algorithms/$slug via the
TanStack <Link>. Reuse the existing badge/chip/ring components from batch 01 — do not create new
variants of any of them.
Algorithms with hasModule(slug) false still appear, with a small mono "visual coming soon" note.

Left rail on xl+: category tree with per-category mastery bars from useCategoryMastery().
Results count line in mono, "clear all filters" link that resets the search params, EmptyState on
no match. Do NOT virtualise — 24 items never needs it.
```

**Accept when:** `/explore?q=graph&category=graphs&status=in-progress&sort=recommended` survives a
reload with identical results, hovering four cards animates at most three, and no card crashes.

---

## PROMPT 5.4 — `/app` dashboard

```
Wire the EXISTING `/app` route in src/routes (open it first — already coded as a static page).
Every number comes from progressStore selectors or src/data. Zero hardcoded stats.

DESIGN LOCK: keep the existing grid, card order, spacing and className strings. Replace fake
values with real ones and attach behaviour. Do not redesign, do not reorder the cards.

- Greeting row: time-aware greeting with the user's name from src/data/user.ts, today's date in
  mono, and a StreakPill (flame + current streak; amber when today's activity row is missing, teal
  when present). Call touchStreak() exactly once per mount, in an effect, not during render.
- ContinueCard (the hero, spans 2 cols): render nextBestAction() from src/lib/recommend.ts — do not
  rewrite that priority logic here. Show <AlgorithmThumbnail slug /> (static, no hover animation),
  the returned `reason` string, a ProgressBar, and one primary CTA to the returned href.
- StatCard row: xp + level with a ProgressBar from src/lib/xp.ts progressPct, current streak,
  algorithms mastered as `n / algorithms.length` (never the literal 24), minutes this week summed
  from activity.
- DailyQuests: 3 quests from progressStore.quests with progress bars; Claim enabled only at 100%,
  calling claimQuest and awardXp. Feedback is a scale pop + toast. No confetti.
- ReviewCard: useDueCardCount(), "oldest card waiting N days", CTA to /review, EmptyState at 0.
- ActivityHeatmap: last 12 weeks from activity, 5 intensity steps from viz-idle to accent, tooltip
  per day, a legend, and cells that are real focusable <button>s with aria-labels like
  "12 March: 40 xp, 3 algorithms". Weeks as columns, oldest left.
- WeakSpots: the 3 lowest-mastery categories from useCategoryMastery(), each with a "Drill this"
  link to /explore?category=<slug>.
- RecentlyViewed: last 4 algorithms by lastSeenISO.

Zero-activity variant: replace ContinueCard (only that card) with "Take the 2-minute assessment"
→ /onboarding/goals. While the store rehydrates, render the existing Skeleton components — never a
bare spinner, never a layout that jumps when data lands.

Links to /review and /onboarding/goals must point at the real existing routes. If a target route
does not exist yet, link to it anyway with the correct path — do not invent a placeholder route
and do not disable the button.
```

**Accept when:** watch 20 steps on `/algorithms/bfs`, return to `/app`, and xp, heatmap, minutes
and RecentlyViewed have all moved — then reload and they are still there.

---

## PROMPT 5.5 — One search index, Cmd-K palette, `/search`

```
Build the search layer over ONE index. Reuse the existing /search route file in src/routes.

`src/lib/search.ts` — pure, no React:
  type SearchDoc = { id:string; kind:'algorithm'|'lesson'|'problem'|'path'|'post'|'help';
    title:string; subtitle?:string; keywords:string[]; href:string }
  Expose registerSource(kind, () => SearchDoc[]) and buildIndex()/searchIndex(query, opts) so a
  later batch can add a source without editing the scorer. Register ONLY the data files that
  actually exist in src/data today — read the folder first. Do NOT create posts.ts or help data
  here; batch 02 registers posts itself.
  Scoring, in this order: exact title match > title prefix > word-boundary match in title >
  match in keywords/subtitle > fuzzy subsequence on title. Then boost by kind in the order
  algorithm > lesson > problem > path > post > help. Deterministic ties broken alphabetically.
  No external search library. Case- and diacritic-insensitive. Empty query returns [].

`src/components/common/CommandPalette.tsx` — the shadcn Command dialog, opened by Cmd/Ctrl-K from
any route (mount it once in the root layout, and wire the existing AppShell search button to the
same open state — do not add a second search input). Results grouped by kind with lucide icons,
full keyboard navigation, Enter navigates via the TanStack router, Esc closes and restores focus to
the trigger. Recent searches in localStorage under 'algora-recent-search', capped at 5. Empty query
shows 5 suggested algorithms from sortRecommended(). Quick actions: "Start today's review",
"Random algorithm" (only slugs where hasModule is true), "Open settings".
The Cmd-K handler must not fire while focus is in an input, textarea, select or contenteditable —
match the guard already used in src/hooks/usePlayerKeys.ts.

`/search` — full page reading ?q= via validateSearch/useSearch (not useSearchParams), kind filter
tabs with per-kind counts, matched substrings highlighted with <mark> styled from our tokens, and a
"nothing found → browse Explore" EmptyState. Typing updates ?q= with navigate({ replace: true }).
```

**Accept when:** Cmd-K opens on `/`, `/explore` and `/algorithms/bfs`; it does NOT open while you
are typing in the visualizer's input tab; Enter navigates; `/search?q=short` deep-links correctly.

---

## PROMPT 5.6 — `/paths` and `/paths/$slug`

```
Wire the existing `/paths` route and add `/paths/$slug` as a TanStack file route
(src/routes/paths.$slug.tsx) following the conventions of the neighbouring route files.
Data: src/data/paths.ts + progressStore. Read paths.ts first and use its real field names.

DESIGN LOCK: /paths already exists as a coded static page — keep its markup and classes and only
attach data and links. /paths/$slug is new: build it from the SAME card, ring, chip, badge and
progress components the rest of the app uses. No new visual vocabulary.

/paths — 4 path cards: title, subtitle, weeks, audience, outcome bullets, algorithm count, a
difficulty spread bar, a preview of the first 3 modules, and per-path progress from progressStore
for signed-in-looking state. Card links to /paths/$slug.

/paths/$slug — hero: title, subtitle, weeks, total minutes, total xp, "Start path" primary CTA
that calls setActivePath(slug) then navigates to the path's first incomplete item. Then an outcomes
list, then a vertical module timeline: each module is a card listing its items (algorithm / lesson /
problem, each with a kind icon, minutes, and a completion check read from progressStore), plus a
module progress ring. Modules whose prerequisites are unmet render LOCKED — reduced opacity, lock
icon, and a tooltip naming exactly what unlocks them. Never hidden, never a dead click.
Sticky right rail on xl+: overall progress ring, "next item" CTA, an estimated finish date computed
from the user's median daily minutes over the last 14 days of `activity` (show "—" when there is
not enough data instead of a fantasy date), and a "set as active path" toggle bound to
progressStore.activePathSlug.
Unknown slug → the app's existing 404, not a crash and not an empty page.
```

**Accept when:** setting an active path on `/paths/<slug>` changes the ContinueCard on `/app`, and
`/paths/not-a-real-path` shows the 404.

---

## Batch 05 acceptance checklist

- [ ] `npm run test` green: 40 engine + builder + xp + progressStore + recommend
- [ ] `/explore?q=graph&category=graphs&status=in-progress&sort=recommended` survives reload
- [ ] All 24 algorithms render on `/explore`; the 12 without engine modules show the fallback
- [ ] Hovering many cards animates at most 3; scrolling stays smooth
- [ ] Dashboard numbers move after watching steps, and survive a reload
- [ ] ContinueCard reaches all four branches (test by clearing lessons / forcing a due card /
      setting an active path / wiping everything)
- [ ] Streak does not break across a 23:50 → 00:10 session
- [ ] Cmd-K works on every route and is suppressed inside inputs
- [ ] Heatmap cells are keyboard-reachable with meaningful aria-labels
- [ ] Locked path modules are visible-but-locked with a stated reason
- [ ] Exactly one progress store; prefsStore still owns speed/reducedMotion/narration
- [ ] No file in `src/components/viz` imports a store, the registry, or @tanstack/react-router
- [ ] `/algorithms/bfs`, `/visualizer` and `/dev/engine` unchanged in behaviour
- [ ] Zero new hex values; zero `any`; no dark panels

## Repair prompts

| Symptom | Paste this |
|---|---|
| A second progress store appeared | `Delete the duplicate store. All progress reads and writes go through src/stores/progressStore.ts. Change nothing else.` |
| Explore filters lost on reload | `Move every filter into the route's validateSearch schema, initialise from useSearch, and write with navigate({ search, replace: true }). Do not use useSearchParams or history.pushState.` |
| Thumbnails tank scroll performance | `Cap concurrent animating thumbnails at 3, compute each run once into a module-level Map, and never compute a run before the card is visible.` |
| Dashboard still shows fake numbers | `Every value on /app must come from progressStore selectors or src/data. Remove all hardcoded stats. Do not change the layout.` |
| Lovable restyled the static page | `Revert the styling of <file> to the previous version and reapply only the behaviour. Keep the original JSX structure and className strings verbatim.` |
| Card crashes on a metadata-only algorithm | `AlgorithmThumbnail must check hasModule(slug) first and render the bg-tint fallback when false.` |
| Streak resets randomly | `touchStreak must compare LOCAL calendar days, not elapsed hours. Keep the vi.setSystemTime tests green.` |
| Recommendation logic duplicated on /app | `Delete the local scoring on /app and call nextBestAction from src/lib/recommend.ts.` |
| Cmd-K hijacks the visualizer input | `Guard the Cmd-K handler exactly like src/hooks/usePlayerKeys.ts: ignore the event when focus is in input, textarea, select or contenteditable.` |
