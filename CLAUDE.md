# Hardik's Portfolio — handoff context

This file is a full handoff so a fresh Claude Code session can keep working on this
without Hardik re-explaining anything. Read it before touching the code.

## What this is

A hand-written, single-file HTML portfolio site for Hardik Gupta (UI/UX designer,
Bangalore, goes by "dost"). Concept: **"a designer's working canvas."** Light theme
only. The whole site — HTML, CSS, JS, fonts, every image and screenshot — is inline
in one file, `index.html`. No build step, no framework, no dependencies. That's
deliberate: it deploys to Vercel (or any static host) by just pointing at the repo
root, zero config.

**Do not split this into multiple files or introduce a build step unless Hardik
explicitly asks for it.** Keep editing it as one HTML file with direct string edits.
It's currently ~3.8MB (most of that is base64-embedded screenshots/videos of his
real apps — that's intentional, see "Asset philosophy" below).

## Repo / deploy

- GitHub repo: https://github.com/User01005/haha.hardikk
- Intended flow: edit `index.html` → commit → push → Vercel (connected to the repo)
  auto-deploys. No `package.json`, no build command needed — Vercel should serve it
  as a static site (set the framework preset to "Other" and output directory to
  the repo root, or just make sure `index.html` sits at the root).

## Brand system

- Pillar colours: `#FFD44E` (yellow), `#E7BBFF` (purple), `#B4F6B8` (green),
  `#A9DEFF` (blue). Each project on the home page is assigned one as its identity
  colour ("pillar"), used for its stool card, its case-study accent, everything.
- Fonts: **Geist Pixel** for display/headings, **Satoshi** for body text,
  **Missing Lovely** for handwritten/annotation-style text.
- Light theme only — no dark mode toggle, no dark mode variant.

## Standing rules (apply to everything, always — these came from direct instruction,
not preference, don't relitigate them)

- Never quote timelines ("2 weeks", "in a sprint", etc.) — don't know how long
  anything actually took, don't imply otherwise.
- No fake testimonials, ever.
- No prices/pricing anywhere.
- Don't foreground Flipkart (he did contract work there via an agency; it's not the
  story of this portfolio).
- Copy is plain and human. No dashes as punctuation, no AI-tone phrases, no
  corporate buzzwords, no filler.
- Give dimensions in px when discussing layout.
- Never commit or expose real API keys. If any `.env` or secret ever ends up in
  this repo, delete it before it's pushed, and say so.
- Blunt, direct feedback expected and wanted — push back on weak ideas rather than
  agreeing by default.

## The case-study architecture (mandatory pattern for every project)

This is the single most important structural rule in the codebase. Every project's
home-page card and case study follow the same system, established with the first
project (Alter) and reused for every one since (Track It, Washio). **Any new
project must follow this exact pattern — don't freelance a new structure.**

### Home page card
- The card's cover shows the app's **real home screen**, filling the card
  edge-to-edge (no colour bleed around it).
- A live hotspot (a "+" or the app's real primary action) reveals a second real
  screen via a `clip-path: circle()` wipe, animated in the app's own accent colour
  — never borrowed from another project's colour.
- Only add a pulsing indicator on the hotspot if it represents a **real**
  interaction point in the product. If the card has no real tappable affordance to
  point at (e.g. Washio, which is design-only), don't fake one — just let the
  video/animation do the work, no pulse.
- On the home page, before a card leaves the stool, it shows a solid colour cover
  (its pillar colour) with a launcher-style icon tile. As it rises into the row,
  the cover peels away to reveal the real screen underneath. This is driven by
  `--peel` (a CSS var computed from scroll progress) — see the `.card-cover` /
  `ICON` map in the code.
- A **black stroke** (`box-shadow: inset 0 0 0 1.5px rgba(19,19,22,.9)`) frames
  just the screen area of the card (`.card-top` / `.wcard .top`), not the text
  panel below it. This must survive every state the card passes through: docked
  in the top nav, falling, sitting on the stool, and fully revealed.

### Case study page (`renderCase(p)` / `renderRich(p,c)` engine)
Every case study is data, not new markup — you add a project by writing
`CS["<id>"] = {...}` and `ACV["<id>"] = {html:function(p){...}}` (the canvas cover)
and the generic renderer builds the page. The section order, when the underlying
material exists, is:

1. **Cover canvas** — a Figma-style pannable/zoomable canvas, breakout width past
   the narrow 820px text column (it uses the site's `.acv`/`acv-world` canvas
   engine — reuse it, don't rebuild).
2. **Problem** — the friction being solved, in plain terms.
3. **Personas** — only if real personas exist for that project. If they don't
   (e.g. Washio), skip the section entirely. Never invent personas.
4. **Paper sketches / wireframes + build pipeline chips** — real sketches only.
   Don't dump every sketch that exists — pick the practical few that actually show
   a decision changing (this project trims 19 real sketches down to 5, 9
   wireframes down to the site map's real branches). If there's a Figma file with
   the full set, say so and let the CTA/links point there instead of duplicating
   everything on the page.
5. **Before/after iterations** — only if real earlier versions exist; otherwise
   skip, never fake it. Tags read **"v1 · Figma"** / **"Final"** (this replaced an
   earlier "Earlier/Final" wording — keep the new pattern), rendered via
   `itersHTML(list, tags)`.
6. **Components** — real Figma/asset component sets, plus **2–3 live rebuilt
   interactive demos** that match the product's actual behaviour/motion/data (not
   generic mockups). Only include components that are actually real and earn their
   space — cut anything decorative, non-interactive, or not actually used in the
   product (this project dropped a "nav bar" and "search bar" component set once
   it turned out the real app has neither).
7. **Final screens board** — a scattered depth-parallax stage/lightbox (reuse
   `stageHTML()`-style pattern), the real shipped-equivalent screens.
8. **"Three things in, one thing out"** flow diagram.
9. **"How it moves"** — numbered steps.
10. **Design system** — real colours/type sourced from actual code or a spec
    sheet. Never invent hex values.
11. **Decisions grid** — the calls that mattered, with the actual reasoning.
12. **"What I cut" / "What I'd do differently"** — honest, specific.
13. **CTA + links** — if there's a live prototype (Figma or otherwise), link it
    directly and say the on-page content is a gist, not the whole archive. If
    there's real code, link GitHub and offer the APK; if design-only, say so
    plainly (no APK, no GitHub link, no implication it shipped).

Each case study uses its own accent colour scoped via
`#v-case[data-case="<id>"]{--acc:...}` instead of reusing another project's colour.

### Keep it light
There's a Figma file (prototype + full frame set) behind every case study. The
on-page case study is **a gist, not the archive** — don't embed every sketch,
every iteration, every screen "just in case." Every asset that isn't actually
referenced anywhere on the rendered page should be dropped before it's embedded,
not just hidden with CSS. This mattered concretely: trimming genuinely unused
assets cut Washio's case study from 4.55MB to 3.84MB with zero visual loss.

## Current project state (as of this handoff)

Live/complete case studies, each following the pattern above:
- **Alter** — personal AI assistant, Android, real code exists, real GitHub link,
  real APK offer. Yellow (`#FFD44E`) pillar, black-and-yellow UI. This was the
  first case study and set the whole pattern — when in doubt about how something
  should look or behave, check what Alter does.
- **Track It** — expense tracker, Android, real code (GitHub:
  `https://github.com/User01005/Trackit`), real APK offer. Blue (`#A9DEFF`)
  pillar. Live components are ported from the actual Kotlin source (voice parsing,
  spring physics values, category colours) — not invented.
- **Washio** — laundry app, **design only, no code**. Green pillar. No APK, no
  GitHub link (there's no repo). CTA points to the Figma prototype and file
  directly (see links below), plus a Notion doc with the full product/UX writeup.
  Its former product name ("Laundry Mate") must never appear anywhere — that was
  explicitly removed and should stay removed.
  - Figma prototype: `https://www.figma.com/proto/m3SUyrHxJpr0Kd4u7X1NS3/Portfolio?node-id=1-11475&viewport=-114018%2C2820%2C1&t=atcKJfUxhiDERAnD-1&scaling=min-zoom&content-scaling=fixed&starting-point-node-id=1%3A11475&show-proto-sidebar=1&page-id=0%3A1`
  - Figma file: `https://www.figma.com/design/m3SUyrHxJpr0Kd4u7X1NS3/Portfolio?node-id=1-35294&t=0ph4ZnQHoDeBoTTq-11`
  - Notion doc: `https://jealous-ghost-a9e.notion.site/Laundry-App-Product-UX-UI-Design-3c87ea0aa24480f8b54ff007f0655dcb?source=copy_link`

Still placeholder / not yet built out to the full pattern:
- **Parchi** — Kirana voice billing app, "In beta." Needs real screens and a full
  case study; currently a stub card on the home page.
- **Hourbit** — focus timer, side project he uses daily. Currently a small/simple
  card treatment, not a full case study — confirm with Hardik whether it needs one
  or stays a lightweight mention.

## Known open item: performance

Hardik has reported lag/jitter on his own machine: the home→work page transition
feels laggy, and the moment cards leave the stool and settle into the row (the
scroll-driven reveal) feels jittery — small scrolls cause a visible stutter/bounce.

What's been checked and ruled out or fixed already:
- The scroll-driven animation loop is already `requestAnimationFrame`-driven with
  passive scroll listeners — not a naive/blocking implementation.
- There was a large, unused "page travel" transition function (deep-cloning a
  whole view's DOM for a blur/zoom effect) — it was dead code, never actually
  wired to navigation, so it wasn't the cause.
- Washio's canvas used to autoplay a video on every visit; that video is gone
  entirely now (replaced with a static gradient + wordmark).
- Washio's card-cover video used to autoplay unconditionally the moment the work
  grid mounted; it's now gated behind an `IntersectionObserver` and only plays
  while its card is actually on screen.

What's still unverified: whether the above actually fixes the felt jank on
Hardik's real hardware. Testing so far has been in a headless/sandboxed browser
without real GPU compositing, so the frame-timing numbers gathered there aren't
trustworthy as a "before/after" — they're noisy run to run. **Ask Hardik to check
`chrome://gpu` on his machine and confirm hardware acceleration is actually
enabled** before assuming there's more code-level work to do — a site this visually
heavy (global `corner-shape: squircle` on every element, several stacked
`backdrop-filter: blur()` layers for the progressive scroll blur) will genuinely
struggle on software rendering, and that would explain the symptoms better than
any specific bug found so far.

If more perf work is needed, the next real lever (not yet touched, real
regression risk, needs before/after screenshots and testing) is narrowing the
global `*,*::before,*::after{corner-shape:squircle}` rule to only the elements
that actually declare a `border-radius`, instead of applying it universally.

## How to test changes

There's no formal test suite — this is a hand-built site. Before considering any
change done:
1. Open `index.html` in a browser (or serve it locally) and manually check the
   route(s) you touched.
2. Check both a desktop width (~1440px) and a narrow/mobile width (~390px) for
   horizontal overflow or broken layout.
3. Check the browser console for JS errors.
4. If you have Playwright available, a simple regression pass that's been used
   throughout this project: load each of these hash routes at both 1440 and 390px,
   click once to dismiss the intro gate, wait ~500ms, and check for
   `document.documentElement.scrollWidth > clientWidth` (overflow) and any
   `pageerror` events:
   `#/`, `#/work`, `#/work/alter`, `#/work/track-it`, `#/work/washio`,
   `#/work/parchi`, `#/work/hourbit`, `#/about`, `#/offer`, `#/canvas`, `#/contact`

## Conventions worth knowing before editing

- The site has a hash router: `#/`, `#/about`, `#/work`, `#/work/<id>`, `#/offer`,
  `#/canvas`, `#/contact`. `route()` near the bottom of the script dispatches on
  this.
- Every true-circle UI element needs an explicit `corner-shape: round !important`
  override, because the site sets `corner-shape: squircle` globally. If you add a
  new round button/avatar/dot, remember this or it'll render squircled by mistake.
- Shared base64 asset tables are large JS object literals (`ALT`, and
  project-specific ones like `TRK`, merged in via `Object.assign(ALT, TRK)`
  patterns). When adding a new project's assets, follow that same merge pattern
  rather than inlining `src="data:..."` by hand everywhere.
- External links in case-study CTAs/links rows should open in a new tab
  (`target="_blank" rel="noopener"`) — this is already handled for anything
  starting with `http`.

## What Hardik actually wants from you right now

Read this file, don't ask him to re-explain the project. If something in here is
ambiguous or you're about to make a structural call he hasn't specified, say so
plainly and ask — don't silently improvise on the architecture above, since it's
deliberate and consistent across three projects already. Otherwise, just keep
building: finish Parchi and Hourbit to the same pattern, and look into the
performance question once he confirms his GPU acceleration status.
