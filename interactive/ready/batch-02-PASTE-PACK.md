# Batch 02 — Marketing goes live (paste pack, adapted to the real repo)

Run this **after batch 05**, not before. `00-ORDER-AFTER-04.md` explains why: the landing page's
algorithm showcase is the same component as `/explore`'s card, and 05 owns it.

Snapshot as `discovery-green` before the first paste here.

## Corrections applied vs `interactive/batch-02-marketing-live.md`

| Source doc says | This pack says |
|---|---|
| `src/pages/Landing/sections/` | your routes are TanStack file routes; sections go in `src/components/marketing/`, the route file stays where it is |
| prompt 2.2: HeroDemo is a scripted 12-frame teaser, "remove all imports from src/engine" | **reversed by decision** — HeroDemo runs the real engine on first build. Old 2.2 and deferred 4.5 are now one prompt (2.1) |
| prompt 2.5: five auth screens against a fake 800ms delay | **moved to batch 10**, kept verbatim in the appendix below. Do not paste it in this batch |
| "keeping the existing visual design" (once, in passing) | a DESIGN LOCK line in every prompt. These pages are already approved and signed off |
| `useSearchParams` for blog filters | TanStack `validateSearch` + `useSearch` + `navigate({ search, replace: true })` |
| AlgorithmShowcase builds its own cards | imports `AlgorithmThumbnail` from batch 05, prompt 5.2 |
| blog posts invisible to search | `/blog` registers a `post` source with `src/lib/search.ts` from batch 05, prompt 5.5 |

**Prompt order**

```
2.1  HeroDemo on the real engine
2.2  landing page sections wired
2.3  /pricing /about /contact /campus
2.4  /blog + /blog/$slug
2.5  nav, footer and dead-link audit
```

Auth is not in this batch. `/signup` and `/login` only need to be reachable; batch 10 wires them
once, against Supabase.

---

## PROMPT 2.1 — HeroDemo, on the real engine, built once

```
Create `src/components/marketing/HeroDemo.tsx` — the landing hero's live demo. It runs the REAL
engine. There is no scripted-frames version; do not hardcode frames.

DESIGN LOCK: match the demo panel that already exists in the static landing page — same panel
styling, same proportions, same position. You are replacing its contents with a working demo, not
redesigning the hero.

How it drives itself (this is deliberate — read it before you write):
- Import getModule from src/engine/registry.ts and compute the run ONCE at first visibility:
  getModule('bubbleSort') with the module's first preset. Memoise it in a module-level variable so
  a remount is free. Never call it at import time.
- Hold the current step index in local component state. Do NOT use playerStore, do NOT mount
  useAutoplay or usePlayerKeys — those belong to the workspace's player and mounting them here
  would fight it. This component owns a single requestAnimationFrame ticker with accumulated delta
  time, ~1000ms per step, and it must clean up its frame on unmount. No setInterval, no setTimeout.
- Loop with a ~900ms hold at the last step, then restart at 0.
- Pause when off-screen (IntersectionObserver) and when the tab is hidden (visibilitychange).
- prefsStore.reducedMotion true, or prefers-reduced-motion → render the middle step statically and
  never start the ticker.

What it renders — all from run.steps[i], nothing invented:
- the visual through the existing <FrameView frame={step.frame} /> from src/components/viz. Do not
  write a new renderer and do not restyle FrameView.
- a code pane of run.pseudocode (or codeByLang.js) with the line at step.codeLine highlighted
  bg-tint with a 3px accent left bar — light theme, never a dark editor.
- one line of narration: step.narration.
- mono counters from step.counters, labelled.
- a "Try it yourself →" TanStack <Link to="/visualizer"> in the corner.

Give the panel a fixed aspect ratio and min-height so the landing page never reflows as steps
advance. No props required. Nothing outside this file changes.
```

**Accept when:** the hero demo sorts, loops, freezes when scrolled out of view and when you switch
tabs, and is static under reduced-motion — and `/algorithms/bfs` still plays independently after you
leave the landing page. That last check is the whole point of not sharing the store.

---

## PROMPT 2.2 — Landing page, wired

```
Wire the EXISTING `/` route (src/routes/index.tsx or whatever the repo actually names it — open it
first). Split it into section components under `src/components/marketing/` and make every element
real.

DESIGN LOCK: this is a MOVE, not a redesign. Cut each section's existing JSX into its own file and
paste it unchanged — same elements, same className strings, character for character. The rendered
page must look pixel-identical before and after this prompt. Report anything you had to change.

Sections, in the order they already appear: Hero (mount <HeroDemo /> from 2.1 in the demo slot) ·
LogoStrip · HowItWorks · FeatureGrid · AlgorithmShowcase · PathsPreview · Testimonials ·
PricingPreview · FAQ · FinalCTA.

Make them real:
- AlgorithmShowcase: 8 algorithms read from src/data/algorithms.ts, each card using
  <AlgorithmThumbnail slug animateOnHover /> from src/components/common (batch 05) — do not build a
  second thumbnail component and do not import the registry directly here. Prefer slugs where
  hasModule(slug) is true so every card shows a real visual. Card links to /algorithms/$slug.
- PathsPreview: 4 cards from src/data/paths.ts, linking to /paths/$slug.
- PricingPreview: 2 tiers read from the PLANS constant created in prompt 2.3 — if you run this
  prompt first, read the existing static values and leave a single TODO comment; do not duplicate a
  price list.
- FAQ: the existing questions in a shadcn accordion.
- every button and card navigates with the TanStack <Link>. Zero href="#", zero onClick that does
  nothing.
- section ids kept/added so nav anchors work, with scroll-mt-24.

Motion: framer-motion whileInView opacity+16px rise, once: true, stagger 0.06, and completely
disabled when prefsStore.reducedMotion is true. Do not animate the hero's first paint — LCP text
must render immediately.
```

**Accept when:** you can diff the page against the previous snapshot and see no visual change,
every link lands on a real route, and the file is now sections rather than one long component.

---

## PROMPT 2.3 — `/pricing` `/about` `/contact` `/campus`

```
Wire these four existing routes in src/routes. Open each file first.

DESIGN LOCK: keep every existing layout, section order and className string. You are adding state,
validation and real data.

Forms: check package.json first. If react-hook-form and zod are present, use them. If not, add
those two and only those two — do not introduce formik, yup, valibot or a UI library.

/pricing — one PLANS constant (src/data/plans.ts) holding tier name, monthly price, feature list
and CTA target; monthly|yearly toggle where yearly shows "2 months free" and RECOMPUTES the
displayed price from that one constant (never a second hardcoded list); the existing
feature-comparison table driven from the same constant with Check/Minus icons, collapsed behind a
disclosure under md; the existing billing FAQ in an accordion.
CTAs: Free → /signup · Pro → /signup?plan=pro · Campus → /contact?topic=campus.
Read the current static prices and features out of the page and move them into PLANS verbatim — do
not invent new pricing.

/about — keep the existing copy. Make the "how it works technically" section state the step-log
idea in three plain sentences (executed once as a pure function, emitting an immutable step list;
canvas, code, and explanation are all functions of steps[i]) — this is the real differentiator, say
it out loud. Hiring CTA links to a real target or is removed; no dead link.

/contact — a working form: name, email, topic select (General / Sales / Campus / Support / Bug),
message (min 20 chars), plus a visually-hidden honeypot field that silently discards the submit.
Inline per-field errors with aria-invalid + aria-describedby, submit disabled with a spinner while
sending, a simulated 900ms submit, then a success panel that REPLACES the form. Prefill `topic`
from ?topic= via the route's validateSearch (not useSearchParams). Extract the form as
`src/components/marketing/ContactForm.tsx` with a `lockedTopic?: string` prop.

/campus — keep the existing pitch layout and reuse <ContactForm lockedTopic="campus" />. Do not
write a second form.
```

**Accept when:** the yearly toggle changes both tiers from one constant, `/contact?topic=campus`
opens with Campus preselected, and submitting an invalid form shows per-field errors and never
clears what you typed.

---

## PROMPT 2.4 — `/blog` and `/blog/$slug`

```
Create `src/data/posts.ts` with 9 posts:
{ slug, title, excerpt, category:'engineering'|'learning'|'product'|'interview', tags:string[],
  author:{name,role,initials}, publishedAt:string, readMinutes:number,
  body: Array<{kind:'p'|'h2'|'ul'|'code'|'callout'|'quote', ...}> }
Write real posts, not lorem ipsum — subjects you can actually stand behind: how the step log works,
why scrubbing backwards teaches better than replaying, what students get wrong about Dijkstra, how
to read a complexity table. Three of them should be genuinely technical.

/blog — wire the existing route: search over title + excerpt (debounced 200ms), category filter
chips, sort newest|oldest, one featured card on top, the existing grid for the rest, EmptyState on
no match. State lives in the route's validateSearch schema ({ q?, category?, sort? }), read with
useSearch, written with navigate({ search, replace: true }). Not useSearchParams.

/blog/$slug — new TanStack file route following the conventions of its neighbours. max-w-[720px]
prose column rendering the typed block array with our own components — no markdown parser, no
dangerouslySetInnerHTML. Code blocks are LIGHT themed like the rest of the app. Sticky
table-of-contents from the h2 blocks on xl+ with scroll-spy, a reading-progress bar under the nav,
author box, prev/next links, 3 related posts by shared tags, newsletter CTA. Unknown slug → the
app's existing 404.

Then register posts with the search layer: call registerSource('post', …) from src/lib/search.ts
(batch 05, prompt 5.5) so Cmd-K finds articles. Do not edit the scoring function.

DESIGN LOCK: /blog keeps its existing markup and classes. /blog/$slug is new — build it from
existing typography tokens and components only, no new visual vocabulary, no dark code blocks.
```

**Accept when:** `/blog?category=engineering&q=graph` restores on reload, Cmd-K finds a post by
title, and the TOC highlights the section you are reading.

---

## PROMPT 2.5 — Navigation and dead-link audit

```
Audit only. Do not restyle anything, do not touch page layouts.

1. Marketing header and footer: every link uses the TanStack <Link> with a `to` that resolves to a
   real route in src/routes. Anchor links to landing sections use the real section ids. Mobile menu
   opens, traps focus, closes on Esc and on navigation, and returns focus to the trigger.
2. Search the whole repo for href="#", to="#", empty onClick handlers, and buttons with no action.
   List every one you find, then fix them by pointing at the correct existing route. If a target
   genuinely does not exist yet, keep the correct future path — do not create a placeholder route
   and do not disable the control.
3. The active route gets an aria-current="page" and the existing active style.
4. Report a table of every route in src/routes and whether it is reachable by clicking from `/`.

Change nothing else. This prompt must not produce a single styling diff.
```

**Accept when:** the reachability table has no orphans except `/dev/engine` and the admin routes,
and the mobile menu is keyboard-clean.

---

## Batch 02 acceptance checklist

- [ ] Landing page is pixel-identical to the pre-batch snapshot
- [ ] HeroDemo runs the real engine, loops, pauses off-screen and on tab blur, static under reduced-motion
- [ ] Leaving `/` and opening `/algorithms/bfs` — the workspace player is unaffected by HeroDemo
- [ ] Landing showcase uses AlgorithmThumbnail, not a second thumbnail component
- [ ] Pricing yearly toggle recomputes from one PLANS constant
- [ ] Contact form blocks invalid submits, keeps typed values, honeypot silently discards
- [ ] `/campus` reuses ContactForm with the topic locked
- [ ] `/blog?category=engineering&q=graph` restores on reload; Cmd-K finds posts
- [ ] Blog body renders from the typed block array — no markdown parser, no dangerouslySetInnerHTML
- [ ] Zero dead links anywhere; mobile menu keyboard-clean
- [ ] Zero new hex values, zero dark panels, zero `any`
- [ ] `npm run test` still green

## Repair prompts

| Symptom | Paste this |
|---|---|
| Lovable redesigned the landing page | `Revert src/routes/index.tsx and src/components/marketing/** to the previous snapshot, then re-split into sections by MOVING the original JSX unchanged. Identical classNames.` |
| HeroDemo hijacks the visualizer | `HeroDemo must not import playerStore, useAutoplay or usePlayerKeys. Keep the step index in local state with its own rAF ticker.` |
| HeroDemo recomputes the run every render | `Compute the engine run once into a module-level variable, on first visibility only.` |
| Second thumbnail component appeared | `Delete it. The landing showcase imports src/components/common/AlgorithmThumbnail.tsx.` |
| Two price lists | `Delete the duplicated prices. Both /pricing and the landing PricingPreview read src/data/plans.ts.` |
| Blog filters lost on reload | `Move q/category/sort into the route's validateSearch schema and write them with navigate({ search, replace: true }).` |
| Blog renders raw markdown | `Render the typed block array with our own components. No markdown parser, no dangerouslySetInnerHTML.` |
| A dark code block appeared | `Code blocks use the same light tokens as the visualizer's code pane. No dark backgrounds anywhere.` |

---

## Appendix — moved to batch 10, do NOT paste here

The original prompt 2.5 (auth screens) belongs to batch 10, where it runs once against Supabase
instead of an 800ms `setTimeout`. Kept verbatim so it is not lost. Its screens already exist
statically (`login.tsx`, `auth.tsx`), so the same DESIGN LOCK applies when you get there.

```
Make /signup /login /forgot-password /reset-password /verify-email fully interactive with
react-hook-form + zod.

Shared: an AuthLayout — centred max-w-[440px] card on bg-paper, wordmark on top, title + subtitle,
form, footer link, and a right-hand marketing panel on lg+ showing one rotating student outcome
stat (bg-tint, no dark panel).

/signup — name, email, password with a live strength checklist (length/case/digit/symbol) that
ticks as you type, confirm password, terms checkbox. Read ?plan= and show a "Pro plan selected"
chip. On success → /verify-email?email=…
/login — email, password with a show/hide eye toggle, "remember me", forgot-password link. The
wrong-password error state must be reachable. On success → /onboarding/goals.
/forgot-password — email only → "check your inbox" panel with a resend cooldown timer.
/reset-password — new password + confirm, reads ?token=, invalid-token state.
/verify-email — mail icon, the email echoed from the query param, a 60s countdown resend button,
"wrong address?" back to /signup.

All: proper autoComplete attributes, aria-invalid + aria-describedby on errors, Enter submits, and
no password ever logged to the console.
```
