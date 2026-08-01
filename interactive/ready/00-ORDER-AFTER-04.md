# Order decision — you are standing after 01, 03, 04

Three documents in this repo disagree about what comes next. That conflict is real, not a typo,
and it has to be settled once before you paste anything else.

| Document | Order it states |
|---|---|
| `INTERACTIVE-PLAN.md` | `01 → 02 → 03 → 04 → 05 → 11 → 06 → 07 → 08 → 09 → 10` |
| `ready/batch-03-PASTE-PACK.md` (line 40–44) | `05 → 06 → 07 → 09 → 08 → 10 → 02 → 11` |
| `ready/batch-04-PASTE-PACK.md` | prompt 4.5 (HeroDemo) "deferred to batch 02, which is late in the order" |

`INTERACTIVE-PLAN.md` put 02 second for one reason it states out loud: *"it forces the player into
existence early."* You already built the player in 03 and 04. **That reason is spent**, so the
plan's position for 02 is now obsolete — but batch-03's position (02 after 10, dead last but one)
overcorrects and costs you traffic for the entire build.

---

## The order to run from here

```
05 explore + search + progress store      ← next, paste pack ready
02 marketing live  (WITHOUT the auth prompt)
11 SEO + share
06 lessons + review
07 practice editor
08 gamification
09 onboarding + roadmap
10 backend  +  the auth screens (old prompt 2.5) folded in
```

Four decisions inside that line, with the reason each way:

**1. 05 before 02, not after.** Batch 02's landing page contains `AlgorithmShowcase` — eight
algorithm cards with live previews — and batch 05's `/explore` contains the same card with the
same live preview. Whichever runs first invents that component; whichever runs second either
duplicates it or rewrites it. 05 owns the algorithm catalog, so 05 builds the card
(`AlgorithmThumbnail`) and the landing page imports it. Building 02 first means building that
component twice and giving Lovable a second chance to restyle it.

**2. 02 before 11, and both before 06–09.** `INTERACTIVE-PLAN.md` is right that 11 is the only
batch that brings new people, and `GROWTH.md` is the reasoning behind it. But 11 prerenders and
adds meta to routes that 02 makes real; pointing search traffic at a landing page whose buttons
are decorative converts nobody. So: make the public pages work (02), then make them findable (11),
then deepen the learning loop. Running 02 dead last, as batch-03's pack suggests, means every day
of the build has an inert front door.

**3. The auth screens leave batch 02.** Old prompt 2.5 builds five auth forms against an 800ms
`setTimeout`. Batch 10 then rebuilds all five against Supabase. Your static `login.tsx` /
`auth.tsx` pages already look right — nothing is gained by wiring them to a fake backend for the
next eight batches, and you would touch the same five files twice. Prompt 2.5 is therefore moved
verbatim into batch 10, where it runs once, against real auth. `/signup` and `/login` stay
navigable in the meantime; that is all batch 02 needs from them.

**4. HeroDemo runs on the real engine, first time.** Confirmed. Old prompt 2.2 (scripted 12-frame
teaser) and deferred prompt 4.5 (swap it for the engine) collapse into one prompt in the batch 02
pack. There is no scripted-frames stage at all. You touch `HeroDemo.tsx` once.

---

## Design lock — add this to Lovable Knowledge before the next paste

Your marketing, auth, explore, dashboard, search and paths pages already exist as **coded static
pages** and their look is signed off. Every prompt from here on is a *wiring* prompt, and Lovable's
default failure on a wiring prompt is to "improve" the layout while it's in the file. Open
Lovable → Settings → Knowledge and append these five lines to the block you pasted in batch 03:

```
DESIGN LOCK: the pages in this repo are already visually approved. When a prompt adds behaviour to
an existing page, reuse that page's existing JSX and className strings verbatim. Do not restyle,
re-space, reorder, rename or "modernise" anything the prompt did not explicitly ask you to change.
If a prompt asks you to split a page into section components, MOVE the existing markup unchanged —
copy the classNames character for character. Report any markup you had to alter, and why.
```

That clause is the whole answer to "my first design must not change". Repeat it — the per-prompt
`DESIGN LOCK` lines in the packs are deliberate duplication, because Knowledge alone drifts by
message thirty.

---

## Snapshot discipline

You have `engine-green` (after 03) and, hopefully, a snapshot after 04. Before the first paste of
05, snapshot as `visualizer-green`. Then one snapshot per green prompt. Rolling back one prompt is
free; rolling back a batch you never snapshotted is a rebuild.

## Files in this folder

```
00-ORDER-AFTER-04.md        this file
batch-03-PASTE-PACK.md      done
batch-04-PASTE-PACK.md      done
batch-05-PASTE-PACK.md      next
batch-02-PASTE-PACK.md      after 05 — marketing only, auth removed
```
