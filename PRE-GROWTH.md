# Algora — Pre-release growth plan

> Companion to `GROWTH.md`. That file covers **after** you are live: SEO, 60 doors, retention.
> This file covers the weeks **before** launch — what to show, what to ask for, and how to arrive
> with an audience instead of an empty room.
>
> Scope: everything here is either a document, a video, or ONE new page. Nothing in this plan
> touches an already-approved design. See §9 (Design lock) — that is non-negotiable.

---

## 0. Read this before you build any hype

Three uncomfortable facts, because a plan built on flattery fails quietly.

**Fact 1 — Students do not join waitlists.**
Waitlists work for *status* products (Superhuman, Arc, Clay) where being early is a flex you can
show people. Nobody screenshots "I'm #4,102 for an algorithm tutorial." A student in Trigger A
panic (`GROWTH.md` §1) has an exam Thursday; a waitlist is useless to them and they will never
return. So a pure waitlist strategy has a low ceiling here — plan for **hundreds**, not tens of
thousands, and treat every one as precious rather than expecting volume.

**Fact 2 — Your pre-launch asset is not a promise, it's a working thing.**
Most pre-launch founders have nothing to show, so they make a mystery landing page. You are in a
much rarer position: batches 01, 03 and 04 are done, which means **the engine and the visualizer
actually run.** You can show the product working. That changes the whole strategy — you are not
building anticipation for an idea, you are demonstrating a thing and collecting the people who
gasp. That is a fundamentally stronger position and you should exploit it, not hide it behind a
"coming soon".

**Fact 3 — The single riskiest thing you can do pre-launch is post AI-looking content.**
Your entire Trigger C distribution (devs on Reddit/X/HN) has a hair-trigger for slop. One
AI-morphed UI video, one em-dash-riddled launch post, one fake "Join 40k+ students" counter on a
site with 0 users, and you don't get a second impression from that crowd. Pre-launch, your
credibility is your only inventory. §8 is the anti-slop rulebook — it matters more than the tactics.

**What pre-launch is actually for, then:**
1. Collect ~200–500 people who have *seen the visualizer move* and want in.
2. Land 3–8 professors/club leads (each worth 100× a student signup — `GROWTH.md` §3).
3. Build the video pipeline **once**, so that at launch you can produce 60 clips cheaply.
4. Arrive on launch day with a warm list, so day-1 traffic isn't a cold start.

Not for: vanity counts, a countdown timer, or a mystery brand.

---

## 1. The pre-launch offer (what you're actually asking for)

"Join the waitlist" is a weak ask because it gives the student nothing. Replace it with a trade
where **they get something today**:

> **See Dijkstra run, step by step, right now. Then leave your email and we'll send you the
> 30-day interview roadmap the day it's ready.**

Structure it as a **half-open door**, not a locked one:

| State | What a stranger can do | Why |
|---|---|---|
| **Open, no email, no account** | Play, pause, scrub, and change the input on **3–5 finished algorithm pages** | This is the demo, the proof, and the shareable thing. Gating it kills all three (`GROWTH.md` §2). |
| **Email only** | Get notified when the roadmap + the other ~55 algorithms land; get early access to the study plan | A real future benefit, not a queue position. |
| **Not yet built** | Accounts, streaks, saved progress, practice editor | Do not promise dates on these. |

**Which 3–5 to open first** — pick by search demand and visual drama, in this order:
`dijkstra` → `quicksort` → `bfs` → `binary-search` → `merge-sort`.
Dijkstra first because "dijkstra step by step" is the highest-intent panic query you can win, and
because a graph relaxing is the most *beautiful* thing your engine does — it serves Trigger A and
Trigger C with one page.

**The counter question.** Never fake a number. Options, best first:
1. Show **no count at all** until it's genuinely impressive (>1,000). Silence reads as confident.
2. Show something true and small that isn't a headcount: `"12 algorithms live · 48 in progress"`.
3. If you must show a person-count, show it only past 500 and label it honestly:
   `"612 students waiting"`. Never "40k+" — that string exists in your Blog page *design mock* as
   placeholder copy. **It must not ship as real UI.** Flag it now so nobody wires it up literally.

---

## 2. Video: an honest verdict on your idea

You asked: can I take the GPT-image-2 page designs, turn them into video with Remotion, and post
that to get early signups? Split answer — one half is a bad idea, the other is a good one.

### 2a. Do NOT AI-animate the static page designs
Feeding a UI screenshot to an image-to-video model (Sora / Veo / Kling / Runway) will warp the
text, wobble the hairline borders, and drift the teal. UI is the single worst subject for those
models because a human eye knows exactly what a button is supposed to do. The result reads as fake
within two seconds — and the audience you're courting is precisely the audience that spots it. It
would burn Trigger C for a 20-second clip. **Don't.**

### 2b. The best video you can make requires no skill at all
Your product is *already an animation*. The highest-performing clip you will post pre-launch is a
**silent, 15–25 second screen recording of the real visualizer**, cropped to just the canvas,
looping. No voiceover, no music, no editing, no experience needed.

Why this beats anything produced: it is *proof*. A Reddit comment section can't argue with a thing
that visibly works. `GROWTH.md` §3 already tells you to post recordings rather than links — this is
that, done before launch.

How, with zero experience:
- macOS: `Cmd+Shift+5` → record selection. Windows: Game Bar `Win+G`, or OBS.
- Record at **1280×720 or larger**, then crop to the canvas only. No browser chrome, no tabs, no
  bookmarks bar, no cursor jitter — hide the cursor if you can.
- Run the algorithm at a **slightly slower speed** than feels natural to you. You know what happens
  next; a stranger doesn't.
- 15–25s, ending on the completed state. It should loop cleanly.
- Export MP4 (X, Reddit) and keep a 9:16 crop for Shorts/Reels/TikTok.

**Ship this in week 1.** Everything below is optional polish on top of it.

### 2c. Where Remotion genuinely earns its place
Remotion = React that renders to MP4. You already write React, so it's the one video tool where
your existing skill transfers, and it is deterministic (no AI morphing). Three real jobs for it:

1. **Branded wrapper around your screen recordings.** Import the MP4 with `<OffthreadVideo>`, add a
   teal caption bar, the algora wordmark, and a `algora.dev` end card. Turns a raw capture into
   something that looks like a product. This is the one to learn first.
2. **60 clips from one template** (the real payoff). One composition + a data array of algorithm
   names → `npx remotion render` in a loop → one short for every algorithm, on-brand, in an
   afternoon. Hand-editing 60 videos is a project; this is a `for` loop. This is why Remotion,
   and not CapCut.
3. **The launch trailer, built from your static designs — with honest motion.** Here your GPT
   images *are* usable: slide them, cut between them, reveal them behind a wipe, mask a card and
   pop it in. Deterministic 2D motion of a still image is not slop; a hallucinated *redraw* of it
   is. Keep every screenshot pixel-exact and only move it.

**Licensing, check this before you invest a weekend:** Remotion is free for individuals and
for-profit companies with **up to 3 employees**, including commercial use and local rendering. At 4+
employees you need a company license (Creators ~$25/seat/mo, or Automators at ~$0.01/render with a
~$100/mo minimum). As a solo founder you are fine today — just know the trigger point exists.

Getting started, honestly: `npx create-video@latest`, then `npx remotion studio` for a live
preview, `npx remotion render` to export. Learn exactly three APIs — `useCurrentFrame()`,
`interpolate()`, `<Sequence>` — and stop. That's enough for jobs 1 and 3. Budget one weekend, and
**do not start it until the week-1 screen recording is already posted.**

### 2d. Video priority, in order
| Order | Asset | Skill needed | When |
|---|---|---|---|
| 1 | Raw 20s visualizer screen recording, silent, looping | none | Week 1 |
| 2 | 9:16 crop of the same for Shorts/Reels | none | Week 1 |
| 3 | Remotion wrapper: caption + wordmark + end card | low | Week 3 |
| 4 | 45s launch trailer from the static page designs | medium | Week 5 |
| 5 | Templated per-algorithm clip factory | medium | After launch |

---

## 3. The pre-launch page (the only new page you build)

**Do not build a "coming soon" page.** Build the **real home page** — the one already designed and
already coded — and change exactly two things:

1. The primary CTA `Start free` becomes `Get early access` and opens an email capture.
2. `HeroDemo` runs the real engine (already the plan in `ready/batch-02-PASTE-PACK.md`), so the
   first thing above the fold is the product working.

Reasons this beats a dedicated splash page: it's already designed and approved (no new design =
no design drift), it's the page you'll launch with anyway (so no throwaway work), and it shows the
product instead of describing it.

Add only what doesn't exist yet:
- **One route:** `/early-access` — thank-you + share state after submit.
- **One table:** `waitlist (id, email, created_at, source, referred_by, role)`.
  `source` = which post/clip sent them. `role` = `student | professor | other`, because
  professors get a different, human, hand-written follow-up.
- **One OG image** for the domain, so every pasted link unfurls as the teal-on-paper card and not
  a naked URL. Link previews are most of why a link gets clicked (`GROWTH.md` §4.2).
- **A `/changelog` page** — see §5. This is the build-in-public surface, and it's already listed
  in your designed footer under PRODUCT, so it costs you no design decisions.

Email storage: one table in the DB you're already adding in batch 10. Don't bolt on a separate
email SaaS pre-launch; you'll have <500 rows and you need them joinable to real users later.

---

## 4. Six-week pre-launch calendar

Assumes batches 05 and 02 land in weeks 1–2 (per `interactive/ready/00-ORDER-AFTER-04.md`).

**T-6 — Make one page perfect.**
`/algorithms/dijkstra`, fully playable, no account, real engine. Not five pages at 80% — one page
at 100%. It is your entire pre-launch argument.
Ship: nothing public yet.

**T-5 — Record and post the first clip.**
20s silent recording (§2b). Post to **r/learnprogramming** and X. Title states a fact, not a pitch:
*"I built a visualizer that lets you step through Dijkstra with your own graph"*. Put the link in
a **comment**, not the post body — most subs suppress link posts, and it reads less like an ad.
Ship: clip + first ~50–150 emails, if it lands at all. Many first posts die. Post again next week.

**T-4 — Professors. This is the week that actually compounds.**
Email 20 lecturers who teach CS2/algorithms. Not a pitch deck — a link to the ONE page that maps to
a topic in their published syllabus, and one question: *"would this be useful in your CS2110 unit
on shortest paths?"* Expect 2–4 replies. Each reply is worth more than every student on your list.
Template in §7.

**T-3 — Widen to 5 algorithms + build the Remotion wrapper.**
Quicksort, BFS, binary search, merge sort live. Post the recursion/quicksort clip to **r/leetcode**
and **r/csMajors**. Start the changelog.
Target: ~300 emails cumulative.

**T-2 — Ask the list for one thing.**
First email to your waitlist. Not an update — a **question**: *"which algorithm should I build
next?"* with three options. This does three jobs: it makes the list feel like participants, it
tells you the real demand order, and it verifies your emails actually deliver before launch day
(critical — a broken send on launch day is unrecoverable).

**T-1 — Trailer + launch prep.**
45s Remotion trailer from the static designs. Write the launch post. Line up the professors who
replied — ask them to comment on launch day, don't surprise them.

**T-0 — Launch.**
Email the list **first**, 2 hours before you post anywhere public, so the thread has real comments
from real users before strangers arrive. Then Reddit, then HN (*Show HN*), then X. One day, one
push. Then go straight into `GROWTH.md` §6 weeks 5–8.

---

## 5. Build in public (the cheapest pre-launch channel you have)

You have an unusual amount of *artifact* to show: 8 designed pages, a working engine, a curriculum
plan, 11 build batches. Post the process weekly.

What to post: a before/after of a visualizer bug; the moment the graph animation first worked; the
design system swatches; "here's what shipped this week" against the changelog. Small, specific,
honest, with an image or clip every time.

Why it works pre-launch: you have no users, so you can't post user stories — but you *can* post
progress, and progress is the only content that's abundant right now. It also front-loads Trigger C
relationships so launch day isn't cold.

Where: X for the daily/weekly cadence, `/changelog` as the permanent record, and one longer
retrospective on the Blog at launch. Keep it factual — "shipped the step-scrubber" — not
inspirational-founder voice.

---

## 6. What to measure (and when to stop)

Pre-launch, ignore signups as the headline number. Two numbers matter:

1. **Press-play rate** — of people who land on an algorithm page, what % start the animation?
   Below ~40% means the page isn't inviting the one action the whole product depends on
   (`GROWTH.md` §6). No amount of pre-launch traffic fixes that; fix the page.
2. **Clip → email rate** — of people who watch a clip and click through, what % leave an email?

**Kill criteria, stated in advance so you can be honest with yourself later:**
- Three separate Reddit posts, three different subs, all under ~50 upvotes → the *clip* is the
  problem, not the market. Re-cut it: slower, tighter crop, more dramatic algorithm.
- 20 professor emails, 0 replies → the email is too long or too pitchy. Rewrite to 4 sentences.
- Press-play under 40% after two rounds of fixes → stop all promotion. You have a product problem,
  and promoting into it just burns first impressions you can't get back.

Do not measure: waitlist count as a success metric, impressions, or follower count.

---

## 7. Ready-to-paste assets

### 7a. Professor email (send 20 of these, one at a time, personalised)
> Subject: a step-by-step Dijkstra visualiser for CS2110
>
> Dr. ——,
>
> I'm building a free tool that lets students step through algorithms with their own input —
> here's Dijkstra: algora.dev/algorithms/dijkstra (no signup, works in the browser).
>
> I saw shortest paths is covered in week 7 of your syllabus. Would something like this be useful
> in that unit, or is there a specific step students get stuck on that it should show more clearly?
>
> Happy to add anything that would make it usable in class.
>
> — Gayle

Rules: under 120 words. One link. A question, not a request. Never attach anything. Never BCC 20
people. Reference their actual syllabus or don't send it.

### 7b. Reddit post (r/learnprogramming, clip attached, link in first comment)
> **Title:** I built a visualiser that lets you step through Dijkstra one relaxation at a time with your own graph
>
> **Body:** I kept getting stuck on *why* the priority queue picks the node it picks, and every
> resource either showed a finished diagram or a video I had to scrub. So I built the thing I
> wanted: play/pause/step, editable input, and the code + the queue state stay in sync with the
> animation.
>
> It's free and there's no signup. Five algorithms so far. What should I add next?

Rules: describe the itch, not the startup. Never say "excited to announce", "revolutionise",
"game-changer", or "the future of learning". Answer every comment for the first 3 hours. If you
mention a waitlist at all, mention it once, at the end, in a comment.

### 7c. Waitlist email #1 (T-2, the question email)
> Subject: which algorithm next?
>
> You signed up for Algora early access — thanks. Five algorithms are live and playable right now:
> Dijkstra, quicksort, BFS, binary search, merge sort.
>
> Before I build the next batch: which would actually help you most?
> → A* · red-black trees · dynamic programming
>
> Just reply with one. I read all of them.

Rules: no header image, no HTML template, no "Hi {{first_name}}". Plain text from a real address,
because a plain-text email from a founder gets replies and a designed newsletter doesn't.

### 7d. Prompt to set up Remotion (paste into your IDE agent, not Lovable)
> Set up Remotion in a **separate** folder `video/` at the repo root, isolated from the app so it
> can never affect the app build. Run `npx create-video@latest` using the Blank template.
>
> Create ONE composition `AlgoClip`, 1080×1080, 30fps, 20 seconds, with props
> `{ recording: string; caption: string; algorithm: string }`.
>
> Layout: the screen recording via `<OffthreadVideo src={staticFile(recording)} />` filling a
> rounded 16px white card centred on a `#F7F9F8` background, with a 1px `#E4E9E7` border. Above the
> card, the caption in Instrument Sans `#0E1513`, 48px. Below it, the algorithm name in JetBrains
> Mono uppercase `#0E9C86`, 24px, letter-spaced. Last 2 seconds: fade in an end card with the
> `algora` wordmark and `algora.dev` in mono, using `interpolate()` on `useCurrentFrame()`.
>
> Add a second composition `AlgoClipVertical`, 1080×1920, identical content, card centred with the
> caption above and the wordmark bottom-anchored.
>
> Add npm scripts: `video:studio` → `remotion studio`, and `video:render` → `remotion render AlgoClip out/clip.mp4`.
>
> Constraints: only these colours — `#F7F9F8`, `#FFFFFF`, `#E4E9E7`, `#0E1513`, `#5B6763`,
> `#0E9C86`. No gradients, no glows, no drop shadows beyond a soft realistic one on the card, no
> stock imagery, no emoji, no purple. Do not add Remotion or any video dependency to the app's
> `package.json`.

### 7e. Prompt to add the waitlist to the existing home page (paste into Lovable)
> **DESIGN LOCK — read first.** The home page and all marketing pages are final and approved. Do
> **not** restyle, re-space, re-order, or "improve" any existing markup, className, colour, font
> size, or section. Do not touch the nav, footer, hero layout, or any existing component's styles.
> If any instruction below seems to conflict with the existing design, follow the existing design
> and leave a `// TODO:` comment instead of changing it.
>
> **Task — behaviour only, plus one new route.**
>
> 1. On the home page hero, change **only the visible label** of the existing primary CTA from
>    `Start free` to `Get early access`. Keep its exact classes, size, position and variant.
>    Do the same for the CTA in the final CTA band. Leave every other CTA on the page untouched.
> 2. Clicking it opens the existing Dialog primitive (reuse the app's current dialog component and
>    its current styles — do not create a new modal look) containing one email field and one submit
>    button, styled with the classes already used by form inputs and primary buttons elsewhere in
>    the app. No new colours, no new spacing scale.
> 3. On submit: validate the email client-side, POST to a `waitlist` insert with
>    `{ email, source, role }` where `source` reads a `?ref=` search param if present (default
>    `direct`) and `role` defaults to `student`. Handle three states — loading (disable the button,
>    keep its size fixed so the layout can't shift), duplicate email (treat as success, never show
>    an error), and failure (inline message using the app's existing error text style).
> 4. On success, navigate to a new route `/early-access` built **entirely from existing components**
>    — same nav, same footer, same card and typography styles as the rest of the site. Content: a
>    confirming headline, one line setting expectations, a `Copy link` button that copies the site
>    URL with `?ref=` appended, and a link to the live Dijkstra page labelled so it's obvious they
>    can use it right now. Do not invent new UI patterns for this page.
> 5. Do **not** display any signup counter, queue position, or number of users anywhere.
>
> **Explicitly forbidden:** countdown timers, confetti, testimonials, fake logos, fake statistics,
> the string "40k+" or any invented user count, new fonts, gradients, and any edit to a file that
> isn't strictly required by the five steps above. List the files you changed and why.

---

## 8. Anti-slop rules (apply to every word and frame you publish)

These are the rules that protect the one thing you can't rebuild: a first impression.

**Writing**
- Never: "excited to announce", "revolutionary", "game-changer", "unlock", "supercharge",
  "in today's fast-paced world", "we're on a mission to democratise…".
- Never claim a number you don't have. No "40k+ students" on a site with none. No "trusted by MIT"
  because a *design mock* contained a placeholder logo strip. Those strings are **mock copy** — if
  they reach production as real claims, that's fraud, not marketing.
- State facts: how many algorithms are live, what works, what doesn't yet.
- Write like a student who got frustrated and built something, because that's true and it's also
  the most persuasive framing available to you.

**Visuals**
- Never AI-generate or AI-animate a screenshot of your own UI (§2a).
- Never post a clip with a visible browser tab bar, cursor jitter, or an obvious bug on screen.
- Never use a stock photo of a smiling student. Your brand system already forbids photos of people
  — hold that line in marketing too.
- Every frame you publish uses only the six brand colours. A clip that doesn't look like the site
  teaches people to not recognise the site.

**Behaviour**
- Don't post the same clip to five subreddits in one day. That's how accounts get shadowbanned and
  it's the fastest way to waste your only good asset.
- Don't DM strangers a link.
- Reply to every single comment for the first three hours of any post. This matters more than the
  post.

---

## 9. Design lock (the constraint that overrides everything above)

You already have 8 approved page designs from GPT image 2, and `marketing-remaining.md` defines the
exact system: paper `#F7F9F8`, white cards `#FFFFFF`, hairline `#E4E9E7`, ink `#0E1513`, slate
`#5B6763`, one teal `#0E9C86` / `#14B8A6`. Instrument Sans + JetBrains Mono. No dark panels, no
purple, no gradients, no blobs, no photos of people, no emoji.

**Rules for every pre-launch task:**
1. No pre-launch task may restyle an approved page. Behaviour and copy-in-place only.
2. Every prompt starts with the DESIGN LOCK paragraph from §7e. Paste it every time — an agent
   without that clause will "improve" your hero.
3. New surfaces (`/early-access`, `/changelog`) are assembled from **existing** components only.
   No new visual patterns.
4. Video inherits the same six colours and two fonts. No exceptions for "cinematic" effects.
5. Placeholder copy from the design mocks — "40k+ students", the university logo strip,
   "Dr. Elena Voss", "+31% assignment completion" — is **mock only** and must be stripped or
   replaced with true content before anything is public. Audit for these strings before launch.

---

## 10. One-page summary

| Question | Answer |
|---|---|
| Build a coming-soon page? | No. Ship the real home page with one CTA relabelled. |
| What's the pre-launch asset? | A working visualizer on 1 → 5 public, playable algorithm pages. |
| What do I ask for? | An email, in exchange for the roadmap later — after they've played, never before. |
| First video? | 20s silent screen recording of the real thing. No editing. Week 1. |
| AI-animate my page designs? | No. It will look fake and cost you the dev audience. |
| Remotion? | Yes — for branded wrappers, the trailer, and later 60 templated clips. Free at your size. Week 3, not week 1. |
| Highest-leverage week? | T-4. Twenty personalised professor emails. |
| Realistic list size at launch? | 200–500 warm emails and a few professors. That is a *good* outcome here. |
| Success metric? | Press-play rate above 40%. Not signups. |
| Hard constraint? | The approved design does not change. Ever. |
