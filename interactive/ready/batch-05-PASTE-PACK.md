# Batch 05 — PASTE PACK (rewritten: additive-only, design-preserving)

**This file replaces the earlier version of itself. Read this page before pasting anything.**

Your requirement for this batch is one sentence: **static → interactive, with zero visual change.**
Every prompt below is built around that and nothing else.

---

## 1. Why your design changed, mechanically

Lovable does not *move* code. When a prompt says "split this page into section components" or
"restructure this file", Lovable **re-writes** the JSX from its own understanding of what the page
means. `gap-6` becomes `space-y-6`. `border` becomes `border border-hairline/60`. A wrapper `div`
gains `rounded-xl`. Class order drifts. Each change is tiny; together they are a different page.

So a "DESIGN LOCK" sentence is worthless if the same prompt also asks for a refactor — and the
previous version of this pack did exactly that. The lock has to be a **diff constraint**, not a
plea. That is what §3 is.

**Removed from every prompt in this pack:** any instruction to split, extract, move, reorganise,
or "clean up" an existing file.

---

## 2. What I could not verify, and what I am no longer asserting

I cannot read your Lovable repo from here. The previous version of this pack asserted exact file
paths anyway, which is what made it feel baseless to you. It was.

Worse, your two source documents **contradict each other**, and I have no way to tell which is true:

| Question | `interactive/00-shared-context.md` says | old `batch-05-PASTE-PACK.md` said |
|---|---|---|
| Router | `react-router-dom`, `useSearchParams` | TanStack Router, `validateSearch` / `useSearch` |
| Route folder | `src/pages/` (one folder per route) | `src/routes/` (file routes) |

I will not pick one for you. **Prompt 5.0 asks Lovable**, and every prompt after it says
"use the router this project already uses" instead of naming one.

Everything else the old pack stated as fact — `hasModule()`, `FrameView`, `prefsStore.reducedMotion`,
`usePlayerKeys.ts`, a `/dev/engine` route, vitest being installed, "24 algorithms / 12 engine
modules", `src/data/user.ts` — came from planning markdown, not from your code. Prompt 5.0 confirms
each one. Where a prompt below needs one of these, it is written as
`<from discovery #N>` for you to fill in, or the prompt is written to work either way.

---

## 3. THE LOCK BLOCK — paste into every prompt of this batch, right after your CONTEXT BLOCK

```
DESIGN LOCK — ADDITIVE ONLY. This is the hard constraint of this task.

The existing pages are design-approved and FINAL. You are converting them from static to
interactive. You are NOT redesigning, restructuring, tidying or improving them.

PERMITTED edits to an EXISTING file — only these four:
  1. Add an import line.
  2. Add hooks, variables, handlers inside the component body.
  3. Replace a hardcoded VALUE with an expression, in place.
     e.g.  >1,240<  becomes  >{xp}<     — the surrounding tag does not change.
  4. Add a BEHAVIOURAL prop to an element that already exists:
     onClick, onChange, onKeyDown, value, checked, disabled, href/to, key, ref, type, aria-*.

FORBIDDEN in any existing file:
  - Editing, adding, removing or reordering ANY className. Not one character.
  - Adding, removing, wrapping, unwrapping or reordering ANY JSX element.
  - Moving code into a new file. No extracting, no splitting, no "cleanup".
  - Renaming any file, component, export or prop.
  - Touching tailwind config, index.css / globals.css, design tokens, or components/ui/**.
  - Changing any user-visible text: copy, labels, headings, button text, placeholders.
  - Adding a dependency. If you think you need one, STOP and say so instead of installing it.

SELF-CHECK before you answer: re-read your own diff. If a line you changed contains `className`,
or adds/removes a JSX tag in a file that already existed, you have broken the task. Revert that
line and get the same result using only the four permitted edits.

If a value I asked you to wire has no matching data source, or does not fit the existing markup:
leave the markup untouched and list it under "UNMAPPED". Never invent a source. Never adjust the
markup to fit.

END YOUR ANSWER WITH EXACTLY THIS AND NOTHING ELSE:
  NEW FILES: <paths>
  EXISTING FILES TOUCHED: <path> — <n> lines changed, className changes: 0
  UNMAPPED: <list, or "none">
```

That report line is the important part. `className changes: 0` as a **number you demand back**
makes the constraint checkable by you in five seconds, and makes Lovable audit itself before it
writes. A prose promise does neither.

---

## 4. PROMPT 5.0 — Discovery (READ ONLY — run this first, it writes no code)

```
Read-only task. Write NO code. Modify NO files. Create NO files. Answer as plain text only.

Inventory, exactly and literally — quote from the repo, do not summarise:

1. ROUTER: which routing library is installed (check package.json) and which version? Paste the
   file where routes are declared. Are routes file-based or declared in a component?
2. ROUTE FOLDER: is it src/pages/ or src/routes/ or something else? List its top level.
3. For each of these, say EXISTS or MISSING, with file path and line count:
   /app   /explore   /paths   /paths/:slug (or $slug)   /search   /review   /onboarding/goals
4. ENGINE: list src/engine/ and src/engine/algorithms/. For each algorithm module give its
   exported function name and the exact slug string. Is there a registry/lookup helper
   (something like getModule or hasModule)? Quote its signature or say NONE.
5. DATA: list src/data/. For algorithms.ts give the exact TypeScript type of ONE entry, the total
   number of entries, and every distinct `category` value. Does src/data/user.ts exist?
6. STORES: list src/stores/ with each exported hook name and its state keys. Does any
   progress / XP / streak state already exist anywhere? Quote it or say NONE.
7. VIZ: list src/components/viz/ with each component's props type. Is there a component that
   renders ONE frame of an algorithm (a FrameView or similar)? Quote its props.
8. Which of these already exist, and at what path: StatCard, EmptyState, Skeleton, Chip,
   DifficultyBadge, ProgressBar, RingProgress, StreakPill, and ANY card component that displays
   an algorithm. Quote the props of the algorithm card if one exists.
9. Is there an existing thumbnail/preview component for an algorithm? Path and props, or NONE.
10. TESTING: is vitest or jest installed? What is the test script in package.json? Or NONE.
11. Is there a reduced-motion preference in a store, or is prefers-reduced-motion read directly?
12. Paste the FULL contents of the file that renders /explore. If MISSING, say MISSING.

Do not suggest improvements, do not point out problems, do not offer to fix anything.
Inventory only.
```

**Why #8 and #9 decide the whole batch.** If an algorithm card already exists in your approved
design, batch 05 must **reuse** it. Building a second card is exactly how a catalog page ends up
looking unlike the landing page — the failure you are trying to prevent.

Paste 5.0's answer back to me and I will pin every remaining prompt to it.

---

## 5. PROMPT 5.1 — Progress store (100% new files — cannot touch your design)

Safe to run as soon as discovery #6 says NONE. Touches no existing file, renders nothing.

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Create NEW files only. Modify no existing file. No JSX, no UI, no routes in this prompt.

1. `src/lib/xp.ts` — pure functions, no store import, no React:
     xpForLevel(level: number): number      // xp required to LEAVE this level; 100 * level^1.35, rounded
     levelFromXp(xp: number): number        // level 1 starts at 0 xp
     xpToNextLevel(xp: number): number      // always > 0
     levelProgressPct(xp: number): number   // 0-100
   levelFromXp must be monotonic non-decreasing and must never return 0, NaN or Infinity —
   including for xp = 0 and for absurd input like 1e9.

2. `src/stores/progressStore.ts` — zustand + persist, key 'algora-progress', version 1, with a
   migrate(persisted, version) that returns the DEFAULT state for an unknown version rather than
   throwing. This is the ONLY place learning progress lives; no later batch may add a second one.

   State:
     xp: number
     level: number
     streak: { current: number; longest: number; lastActiveISO: string | null; freezesLeft: number }
     activePathSlug: string | null
     algorithms: Record<string, { status: 'locked'|'new'|'watched'|'learning'|'practiced'|'mastered';
       stepsWatched: number; lessonDone: boolean; quizScore: number | null;
       problemsSolved: string[]; lastSeenISO: string; masteryPct: number }>
     lessons: Record<string, { completedAt: string|null; sectionIndex: number; quizScore: number|null }>
     problems: Record<string, { attempts: number; solvedAt: string|null; bestRuntimeMs: number|null }>
     reviewCards: Record<string, { ease: number; intervalDays: number; dueISO: string;
       reps: number; lapses: number }>
     quests: Record<string, { progress: number; claimedAt: string|null; periodKey: string }>
     achievements: Record<string, { unlockedAt: string|null; progress: number }>
     activity: Record<string, { xp: number; minutes: number; steps: number; solved: number }>  // 'YYYY-MM-DD' LOCAL
     bookmarks: string[]

   Actions: awardXp(amount, reason), recordStepsWatched(slug, n), markLessonSection(slug, i),
   completeLesson(slug, quizScore), recordAttempt(problemSlug), markSolved(problemSlug, runtimeMs),
   touchStreak(), useFreeze(), gradeCard(cardId, grade: 0|1|2|3), setQuestProgress(id, n),
   claimQuest(id), toggleBookmark(slug), setActivePath(slug|null), resetAll().

   Rules:
   - awardXp recomputes level via src/lib/xp.ts and RETURNS { leveledUp: boolean; newLevel: number }.
   - touchStreak compares lastActiveISO to now using LOCAL CALENDAR DAYS, not 24-hour arithmetic
     (23:50 → 00:10 is two days; 08:00 → 23:00 is one day).
     same day = no-op · exactly yesterday = current + 1 · older = reset to 1 unless freezesLeft > 0
     and useFreeze() covers a single missed day. longest = max(longest, current), always.
   - masteryPct is DERIVED, never assigned by hand, recomputed inside every action that changes an
     input: watched 20 + lesson 30 + (quizScore/100)*20 + min(problemsSolved.length/3, 1)*30,
     clamped 0-100, rounded.
   - Every mutation also upserts today's row in `activity`.
   - Unknown slugs passed to any action are ignored: no crash, no phantom record.
   - Do NOT seed demo data. An absent key means "not started"; the selectors handle it.

3. `src/hooks/useProgress.ts` — selector hooks only, each subscribing to the NARROWEST slice so a
   component does not re-render on unrelated changes: useAlgorithmProgress(slug),
   useOverallMastery(), useCategoryMastery(), useDueCardCount(), useTodayActivity(), useStreak(),
   useXp(), useIsBookmarked(slug).
   Read real category values out of src/data/algorithms.ts — never hardcode a category list.
   Every hook must return a sane value for a brand-new user with an empty store.

Then run the TypeScript check and report errors. Do not create a second progress store under any
name, and do not move preference state (playback speed, reduced motion) into this store.
```

**I dropped the demo seeding on purpose.** The old pack said "seed from `src/data/user.ts` so the
app never looks empty in a demo." Seeded numbers make it impossible to tell whether your wiring
actually works, and they are the single most common cause of "the dashboard shows fake numbers."
Build against the true empty state — your empty states are part of the approved design anyway.

---

## 6. PROMPT 5.1b — Lock the two functions that silently corrupt saved data

Only if discovery #10 reports a test runner. If it says NONE, skip this and rely on the checklist.

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Add tests for src/lib/xp.ts and src/stores/progressStore.ts ONLY, using the test runner already
installed (discovery said: <fill in>). New test files only. No component tests, no DOM.
If a test proves a real bug, fix the source — never weaken the test.

xp:
  1. levelFromXp(0) === 1; monotonic non-decreasing sampled across 0…200000.
  2. levelFromXp(xpForLevel(n)) === n + 1 for n in 1…40 (crossing a threshold levels you up).
  3. xpToNextLevel > 0 and levelProgressPct within 0…100 across that range.
  4. No NaN / Infinity for 0, 1, 1e9.

progressStore — drive the store directly, resetAll() in beforeEach, and FAKE THE CLOCK so streak
tests are deterministic in any timezone:
  5. touchStreak twice on the same calendar day leaves current unchanged.
  6. touchStreak at 23:50, then at 00:10 the next calendar day, increments current.
     (This is the test that catches 24-hour arithmetic. It is the point of this prompt.)
  7. Skipping two days resets current to 1 and leaves longest at its previous peak.
  8. Skipping one day with freezesLeft > 0 plus useFreeze() preserves the streak and decrements
     freezesLeft; with freezesLeft === 0 it resets.
  9. awardXp returns leveledUp true exactly on the call that crosses a threshold, false otherwise.
 10. masteryPct === 100 for watched + lesson + quiz 100 + 3 problems; === 0 for a fresh algorithm.
 11. Every action writes today's activity row, and xp accumulates across calls.
 12. An action called with a slug absent from src/data/algorithms.ts creates no record.
```

**Why this one prompt is worth the session:** a streak or XP bug ships into every user's
`localStorage`. You cannot fix the number retroactively — it is already wrong on their machine.
A failure on 6, 8 or 12 is a real bug, not a fussy test.

---

## 7. PROMPT 5.2 — `/explore`: two branches, pick ONE

Run 5.0 first. **Paste discovery answer #12 (the real file) directly above your prompt** — Lovable
preserves markup dramatically better when the current markup is in front of it than when it has to
recall it.

### Branch A — /explore EXISTS (your case, if the design is already coded)

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Task: make the existing /explore page functional. Its layout, toolbar and cards are FINAL.
File: <exact path from discovery #3>. Its current contents are pasted above — preserve them exactly.

Add behaviour only.

1. URL AS THE SINGLE SOURCE OF TRUTH.
   Use the router this project ALREADY uses (discovery #1 — <fill in>). Do not introduce a second
   router, and do not add a search-params library. Follow the pattern of the neighbouring route
   files for reading and writing query state.
   Keys: q, category (repeatable), difficulty, status, sort, view.
   Initialise every control FROM the URL on mount. Write changes back with REPLACE, not push, so
   filtering does not flood browser history. Malformed or unknown values fall back to the default
   instead of throwing. A hard reload must restore the identical result set.

2. Wire each control THAT ALREADY EXISTS IN THE MARKUP to its param:
   search input → q (debounce 200ms, match against name + tags + category, case-insensitive);
   category chips → multi-select toggle with live counts; difficulty control; status filter
   (All / Not started / In progress / Mastered, derived from progressStore); sort
   (Recommended / A-Z / Difficulty / Shortest); grid|list toggle → view.
   Use the selected/unselected styling the markup already has. Do not introduce a new active style.

3. Per-card live data from progressStore, bound to elements that already exist:
   mastery ring/bar value, status, bookmark toggle → toggleBookmark.

4. 'Recommended' sort: put the scoring in a NEW file `src/lib/recommend.ts` (pure, no React) and
   import it. Order: prerequisites satisfied first, then weakest category mastery, then lower
   difficulty, then shorter estimated minutes, then alphabetical as a deterministic final tiebreak.
   Read prerequisite / difficulty / minutes field names out of src/data/algorithms.ts as they
   actually are — do not assume names, and do not edit src/data.

5. Results count and "clear all filters" bind to real numbers / reset the params. If the markup has
   no empty state, report UNMAPPED — do not add one.

Do NOT add virtualization. Do NOT add hover animation — that is the next prompt.
className changes: 0.
```

### Branch B — /explore is MISSING

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Create the /explore page in this project's route folder (discovery #2) and register it following
the conventions of the neighbouring route files.

Compose ONLY from components that already exist in this repo — discovery listed them at #8. Reuse
the existing algorithm card, badges, chips, rings and empty state. Do not fork them, do not create
"v2" variants, do not restyle them. If a piece you need genuinely does not exist, build it using
the same tokens, border, radius and spacing conventions as its nearest existing sibling, and name
that sibling in your answer.

Toolbar, URL state, filtering, sorting and per-card progress exactly as in Branch A above.
```

---

## 8. PROMPT 5.3 — Hover preview (separate prompt, on purpose)

This is the only genuinely risky piece in batch 05: it is the one thing that touches the inside of
your card. It gets its own prompt and its own commit so you can revert it alone.
**Never combine it with 5.2.**

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Add an animated preview to the algorithm card, additively.

There is already a thumbnail/preview element inside the card (discovery #9: <fill in>). Render into
THAT element. Do not resize it, do not wrap it, do not change its classNames, do not change the
card's layout in any way.

Put the logic in a NEW file `src/components/common/AlgorithmThumbnail.tsx`
(props: { slug: string; animateOnHover?: boolean; className?: string }) and render it inside the
existing slot. The card file's diff should be one import plus one element swap inside that slot.

Behaviour:
  - Default: static frame 0, rendered through the EXISTING single-frame viz component
    (discovery #7). Do not write a new renderer.
  - Compute the algorithm's run ONCE, lazily, and memoise it in a module-level Map keyed by slug,
    so ten cards of the same slug cost one run and remounts are free. Never compute at import time;
    compute only after the card has been visible (IntersectionObserver).
  - On hover OR keyboard focus: advance the local step index with a SINGLE requestAnimationFrame
    ticker at 2x (~450ms/step) for 6 steps, then stop and hold. Not setInterval.
  - On mouse-leave / blur: reset to frame 0.
  - Reduced motion (discovery #11 — use whatever this project already does): always static frame 0,
    no ticker at all.
  - Off-screen cards never animate.
  - HARD CAP: at most ONE card animating at a time, enforced by a module-level "currently animating
    slug" guard — not per-card state.
  - This must NOT read or write the global player store. A card preview must never move the
    workspace player. Local state only.
  - Fixed aspect ratio / min-height so the grid never reflows while runs resolve.
  - aria-hidden on the visual; the card already owns the accessible name.

CRITICAL — missing engine modules: some algorithms in src/data/algorithms.ts have NO module in
src/engine/algorithms/ (discovery #4 lists exactly which). For those, render the static fallback
panel and skip animation SILENTLY. Check for the module's existence explicitly before use. Do not
throw, do not console.log, do not wrap it in a bare try/catch.
```

**This is the likeliest crash in the batch.** Your data file lists more algorithms than the engine
has modules. A card that assumes a module exists will white-screen `/explore` — and you would
reasonably blame the renderers instead of the coverage gap.

---

## 9. PROMPT 5.4 — `/app` dashboard

### Branch A — /app EXISTS

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Task: make the existing /app dashboard read real data. Its grid, card order and spacing are FINAL.
File: <exact path from discovery #3>.

For EVERY hardcoded number, label, bar width, ring value and list in that file: find the matching
progressStore value and substitute the expression in place. Same elements, same classNames, same
order. If a displayed value has no real source, leave the markup untouched and list it as UNMAPPED.

Mappings:
  - XP number and level → useXp() + levelFromXp
  - level progress bar → levelProgressPct(xp)
  - streak number → useStreak().current
  - "algorithms mastered n/N" → count of status 'mastered', and the REAL total from
    src/data/algorithms.ts via algorithms.length. Never hardcode 24.
  - minutes this week → sum of activity over the last 7 local days
  - heatmap cells → activity[dateKey], mapped onto the intensity levels the markup ALREADY uses.
    Weeks as columns, oldest left. The cells must be real focusable buttons with aria-labels like
    "12 March: 40 xp, 3 algorithms" — if they are already buttons, only add the aria-label and
    handler; if they are divs, report UNMAPPED rather than changing the tag.
  - recently viewed → algorithms by lastSeenISO desc, sliced to the count the markup already renders
  - weak spots → the lowest-mastery categories from useCategoryMastery(), each linking to
    /explore?category=<slug>
  - daily quests → progressStore.quests; Claim enabled only at 100%, calling claimQuest + awardXp.
    Feedback is a scale pop and a toast. No confetti.
  - review card → useDueCardCount(), "oldest card waiting N days", CTA to /review
  - the "continue" card → the single best next action

NEXT ACTION: add `nextBestAction(progress, data)` to `src/lib/recommend.ts` (created in 5.2) —
returns { kind: 'lesson'|'review'|'path'|'algorithm'; slug; reason; href } | null. Priority exactly:
unfinished lesson > due review cards > next incomplete item of activePathSlug > first non-mastered
algorithm in the weakest category. `reason` is one human sentence, e.g. "you left off at section 3
of 6". Prefer slugs that HAVE an engine module when scores tie, so the card always has something to
show. Returns null only for a brand-new user with zero data. Import it — do not inline this logic
in the page, and do not duplicate the scoring that /explore already uses.

Call touchStreak() exactly once per mount, inside an effect — never during render.

If the file already has a zero-state variant, keep BOTH variants and drive the choice off real data.
While the persisted store rehydrates, use the Skeleton components the page already has; never a
bare spinner, never a layout that jumps when data lands.

Links to /review and /onboarding/goals: point at the real paths even if those routes do not exist
yet. Do not invent placeholder routes and do not disable the buttons.

className changes: 0.
```

### Branch B — /app is MISSING

Use Branch A's mappings, but preface with Branch B's composition rule from §7 — build only from
components discovery #8 reported, and name the sibling you copied for anything new.

---

## 10. PROMPT 5.5 — Search index and Cmd-K

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Two NEW files plus one minimal wiring edit.

1. NEW `src/lib/search.ts` — pure, no React:
   type SearchDoc = { id: string; kind: 'algorithm'|'lesson'|'problem'|'path'|'post'|'help';
     title: string; subtitle?: string; keywords: string[]; href: string }
   Expose registerSource(kind, () => SearchDoc[]) plus searchIndex(query, opts) so a later batch can
   add a source WITHOUT editing the scorer. Register only the data files that actually exist in
   src/data today (discovery #5) — do not import a file that is not there, and do not create new
   data files here.
   Scoring, highest first: exact title match > title prefix > word-boundary match in title >
   match in keywords/subtitle > fuzzy subsequence on title. Then boost by kind in the order
   algorithm > lesson > problem > path > post > help. Ties broken alphabetically for determinism.
   Case- and diacritic-insensitive. Empty query returns []. No external search library.

2. NEW `src/components/common/CommandPalette.tsx` — the shadcn Command component inside a Dialog.
   Opens on Cmd+K / Ctrl+K from any route. Results grouped by kind with lucide icons, arrow-key
   navigation, Enter navigates via this project's router and closes, Esc closes and restores focus
   to the trigger. Recent searches in localStorage key 'algora-recent-search', max 5. Empty query
   shows 5 suggestions from recommend.ts (least-mastered first). Quick actions: "Start today's
   review" → /review, "Random algorithm" (only slugs that have an engine module), "Open settings".
   The shortcut must NOT fire while focus is in an input, textarea, select or contenteditable. If
   this project already has such a guard in a keyboard hook, reuse it rather than writing a second.

3. WIRING — the only existing-file edit permitted in this prompt: mount <CommandPalette /> ONCE in
   the existing app shell/layout, and attach onClick to the search button ALREADY in that shell.
   That is one import, one element appended at the end of the shell's JSX, and one onClick.
   Do not restyle that button. Do not add a second search input anywhere.

Do NOT build the /search page in this prompt.
```

### PROMPT 5.5b — `/search` page

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

If /search EXISTS (discovery #3): wire it only — additive, className changes: 0.
If MISSING: create it in this project's route folder, composed only from existing components.

Reads the q query param via this project's own router API. Kind filter tabs with per-kind counts,
matched substrings highlighted with <mark> styled from existing tokens, and a no-results state
pointing to /explore. Typing updates q with REPLACE, not push.
```

---

## 11. PROMPT 5.6 — `/paths` and the path detail page

```
[PASTE CONTEXT BLOCK]
[PASTE LOCK BLOCK]

Branch per discovery #3: wire if it exists (additive only, className changes: 0), create in this
project's route folder only if MISSING, using the naming convention of the neighbouring route files.

Data from src/data/paths.ts — read it first and use its REAL field names. Completion state from
progressStore.

/paths — one card per path: title, subtitle, weeks, audience, outcome bullets, algorithm count,
difficulty spread, first 3 modules previewed, and real per-path progress. Real counts only.

/paths/<slug> —
  - hero: title, subtitle, weeks, summed minutes, summed xp, and a "Start path" primary CTA that
    calls setActivePath(slug) then navigates to the path's first incomplete item
  - outcomes list
  - vertical module timeline: each module a card listing its items (algorithm / lesson / problem,
    each with a kind icon, minutes, and a real completion check from progressStore) plus a module
    progress ring
  - modules with unmet prerequisites render LOCKED but VISIBLE: reduced opacity, lock icon, and a
    tooltip naming what unlocks them. Never hidden, never removed from the DOM.
  - right rail on xl+: overall progress ring, next-item CTA reusing nextBestAction from
    src/lib/recommend.ts, an estimated finish date from the median daily minutes of the last 14 days
    — if there is no history show "—", never a fabricated date — and a "set as active path" toggle
    writing activePathSlug via setActivePath.
  - unknown slug → the EXISTING 404 route. Do not design a new not-found page.

Build only from the card, ring, chip, badge and progress components the rest of the app already
uses. No new visual vocabulary.
```

---

## 12. Acceptance checklist

**Design first — this is the one you actually care about:**

- [ ] `git diff --stat` for the whole batch shows **zero** changes to the tailwind config,
      `index.css` / `globals.css`, and `src/components/ui/**`.
- [ ] `git diff -- <pre-existing files> | grep className` returns **nothing**.
- [ ] Screenshot `/app` and `/explore` **before** 5.2/5.4 and again after, then flip between the two
      images. Pixel-identical apart from the numbers that were supposed to become real.
- [ ] No file that existed before this batch was renamed, split or moved.

**Then behaviour:**

- [ ] `/explore?q=graph&category=graphs&status=in-progress&sort=recommended` survives a hard reload
      with an identical result set.
- [ ] Filter ten times, then press Back once → you leave the page, you do not step back through ten
      filter states. (Proves replace, not push.)
- [ ] Watch steps on an algorithm → dashboard xp, heatmap, minutes and recently-viewed all move,
      and survive a reload.
- [ ] Clear `localStorage`, reload: empty states everywhere. No `NaN`, no `undefined`, no `0/0`.
- [ ] `nextBestAction` returns each of its four kinds for four hand-made progress states.
- [ ] Cmd-K opens on every route, Enter navigates, and it does **not** hijack while you type in an
      input on the visualizer.
- [ ] Hover four cards at once → at most one animates. Scrolling stays smooth.
- [ ] An algorithm with no engine module shows the static fallback and throws nothing.
- [ ] Heatmap cells are keyboard-reachable with meaningful aria-labels.
- [ ] Locked path modules are visible-but-locked with a stated reason.

---

## 13. Repair prompts

Keep these surgical. Re-describing the feature is what triggers a rewrite.

| Symptom | Paste this |
|---|---|
| **Design drifted** | `Revert your last change to <file> completely. Reapply it using ONLY: added imports, added hooks/handlers, and hardcoded values swapped for expressions in place. Zero className edits, zero JSX structure edits. Report className changes: 0.` |
| It split the page into sections | `Undo that extraction. Restore <file> to one component with its original JSX, then apply only the data wiring. Never move JSX into new files in this project.` |
| It restyled `components/ui/**` | `Revert every change under src/components/ui/. Those are generated primitives and are final. Achieve the change from the calling component instead.` |
| It changed copy | `Restore the original text in <file> character for character. Copy is approved and out of scope.` |
| It installed a package | `Uninstall it and revert. Implement this with what is already in package.json, or tell me it is impossible.` |
| Second progress store appeared | `Delete the duplicate store and its imports. All progress reads/writes go through src/stores/progressStore.ts and src/hooks/useProgress.ts.` |
| Filters lost on reload | `Initialise every filter's state FROM the URL on mount and write changes back to the URL. The URL is the source of truth, not component state.` |
| Back button broken after filtering | `Write filter updates with replace, not push.` |
| `/explore` white-screens | `An algorithm in src/data/algorithms.ts has no module in src/engine/algorithms/. Make the card render its static fallback and skip animation when the module is absent. Check explicitly — no silent try/catch.` |
| Previews tank scrolling | `Cap concurrent card animations at exactly one via a module-level guard, pause off-screen cards with IntersectionObserver, and disable animation entirely under reduced motion.` |
| Card preview moves the main player | `Card previews must not read or write the player store. Give the thumbnail its own local frame state.` |
| Dashboard shows fake numbers | `Every displayed value must come from a src/hooks/useProgress.ts selector. Remove all hardcoded and seeded stats. If a value has no real source, list it as UNMAPPED instead of inventing one.` |

---

## 14. Sequence

```
5.0  discovery         → paste the answer back to me
     commit / snapshot
5.1  progress store    → commit
5.1b tests             → commit        (skip if no test runner)
5.2  /explore wiring   → SCREENSHOT COMPARE → commit
5.3  hover preview     → SCREENSHOT COMPARE → commit
5.4  /app wiring       → SCREENSHOT COMPARE → commit
5.5  search + Cmd-K    → commit
5.5b /search           → commit
5.6  paths             → commit
```

One prompt per Lovable message. Never merge two. **One prompt, one commit** — a design regression
caught at commit 3 costs one revert; the same regression caught at commit 9 costs the batch.

Batch 02 gets rewritten with this same lock once 5.0 tells us what the marketing pages really are.
