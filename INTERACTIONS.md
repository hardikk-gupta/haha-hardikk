# Interactions brief: everything added in rounds 19, 20 and 21

A hand-off for another Claude session working on Hardik's portfolio (`index.html`, one hand-written file, no build step). Hardik will cross out the ones he doesn't want. Build only what's left.

## Where the working code is

Every item below is already built and tested in this repo. Copy it rather than rebuilding it.

- Repo: https://github.com/hardikk-gupta/haha-hardikk
- File: https://github.com/hardikk-gupta/haha-hardikk/blob/main/index.html
- Round 19 (items 0 to 14): https://github.com/hardikk-gupta/haha-hardikk/pull/1 (merged)
- Round 20 (items 15 to 23): https://github.com/hardikk-gupta/haha-hardikk/pull/2 (merged)
- Round 21 (items 24 to 31): https://github.com/hardikk-gupta/haha-hardikk/pull/3 (merged)

All three rounds are in `main`, so the file linked above has every item.

In `index.html` each round is two blocks, found by searching for these comments:

| Round | CSS block (just before `</style>`) | JS block (just before `window.__ok=true;`) |
|---|---|---|
| 19 | `/* ===== ROUND 19: things to find ===== */` | same comment, an IIFE |
| 20 | `/* ===== ROUND 20: the jaw droppers ===== */` | same comment, an IIFE |
| 21 | `/* ===== ROUND 21: the ones that sell the work ===== */` | same comment, an IIFE |

Each JS block is a self-contained `(function(){ ... })();` that lives inside the site's outer script IIFE, so it can read the site's own variables (`P`, `ALT`, `route`, etc.). Inside each round, every interaction is its own sub-IIFE headed by a numbered comment. To drop one, delete its sub-IIFE and its CSS lines, and remove its entry from the `LIST` in item 0.

## Why this lists 30 when the counter says 28

The on-page "found" counter shows 28. It counts one thing Hardik already had (typing "hardik"), and it leaves out three ambient effects nobody can be "caught" discovering: reading ink (9), favicon minimap (19) and address bar progress (29). That makes 30 built interactions, plus the counter itself as item 0.

## Rules every item follows (keep these if you rebuild anything)

- Desktop-only items are gated on `matchMedia("(hover:hover) and (pointer:fine)")`. Anything animated checks `prefers-reduced-motion` and falls back to no animation.
- The site sets `corner-shape: squircle` on everything, so every true circle added here has `corner-shape: round !important`.
- Only site tokens and fonts are used: `--ink #131316`, `--paper #FAFAF8`, the pillars `#FFD44E #E7BBFF #B4F6B8 #A9DEFF`, and `--pixel` (Geist Pixel), `--body` (Satoshi), `--hand` (Missing Lovely). Figma-flavoured UI (redlines, layers, dev mode) borrows Figma's own blue `#0D99FF`, red `#F24822` and green `#14AE5C` on purpose.
- Copy is plain, with no dashes as punctuation. The one intentional exception is item 7 (see below).
- Scroll and pointer work is done in `requestAnimationFrame` with passive listeners, and heavy overlays only run while they're open.
- Anything typing-related ignores keys while the focus is in an `input`, `textarea` or `contentEditable`.

### Keyboard map (so nothing collides)

| Key | Item | Notes |
|---|---|---|
| `/` | 1 Cursor chat | |
| `Shift + O` | 12 Outline mode | |
| hold `Alt` / `Option` | 16 Measure | |
| `L` | 17 Layers panel | |
| hold `C` | 18 Code lens | |
| `P` | 26 Present mode | case study pages only |
| `D` | 28 Dev mode | ignored on `#/canvas` |
| `Shift + H` | 30 Version history | |
| `Cmd/Ctrl + C` while hovering a doodle | 24 Copy to Figma | only when no text is selected |

These were already taken by the site: `V`, `P`, `X`, `1` to `5` for the hero pen tools (only while the hero is on screen), `Ctrl/Cmd + Z` for the plane doodle, and typing `hardik`.

### z-index map

```
toast 130 · history 129 · dev panel 128 · dev highlight/tag 127 · selection toolbar 126
cursor chat 125 · chat bubble 124 · code lens 123 · outline tag 122 · found sheet 121
found pill 120 · measure 119 · two-window rope 118 · layers 117 · layers highlight 116
slingshot 115 · resize ruler 114 · present mode 300 (covers everything)
```

The site's own `.chrome` nav sits at 70, `.eblur` at 55 and `.toast` at 95. That's why the new toast is a separate `.hg-toast`.

### Globals this adds

| Global | What it is |
|---|---|
| `window.hgFound(key)` | marks an item as found (item 0) |
| `document` event `"hgfound"` | fired with `detail` = the item's name (item 30 listens for it) |
| `window.hgCmd(word)` | lets later rounds add cursor-chat commands (item 1) |
| `window.hgPenReset(h1)` | used by item 7 to cancel item 2 edits |
| `window.hgDoodleSVG(el)` | helper from item 24 |
| `window.hgParchiParse(text)` | parser from item 27, handy for testing |
| `window.hgDev(on)` | toggles item 28 |
| `window.hardik` | item 31 |

---

## 0. The "found" counter (the thread that ties it all together)

**What it does:** A small pill fixed bottom left reads `? 3/28 found`. Clicking it opens a sheet titled "This site has a few secrets." that lists every hidden interaction with a one-line hint. Found ones get a green tick and a struck-through title. Each first discovery pops a toast at the top ("found it · Slingshot · 12 of 28") and the pill does a small bounce. The sheet's footer mentions the two effects that aren't counted (tab icon and address bar).

**How it's built:**
- A `LIST` array of `[key, title, hint]` entries.
- On touch devices it is filtered to drop the desktop-only keys (chat, type, lean, outline, mark, twin, measure, layers, lens, resize, svg, drag, dev, history, console), which leaves 13.
- Found state lives in `localStorage["hg-found"]` as an object of keys, wrapped in try/catch because it's a per-viewer convenience only.
- `found(key)` is a no-op if the key is already found. Otherwise it saves, repaints the pill and sheet, shows the toast, and dispatches `hgfound`.
- The pill lifts 58px (`.up`) while the footer is on screen, using an `IntersectionObserver` on `.foot`, so it doesn't cover the copyright line.
- In canvas mode (`body.cvmode`) it sits higher, to clear the canvas toolbar.

**Depends on:** nothing. Every other item depends on this one, so build it first. If Hardik drops the counter, replace `found()` with a no-op.

---

## Round 19: copy you can poke at (items 1 to 14)

### 1. Cursor chat (press `/`)
**What:** Like Figma's cursor chat. Pressing `/` anywhere opens a dark speech bubble stuck to the cursor. Type and press Enter, and the text floats up in a pillar-coloured bubble and fades over about 4s. Typing a page name (`work`, `about`, `services`, `canvas`, `contact`, `home`, or a project: `alter`, `track it`, `washio`, `parchi`, `hourbit`) also navigates there, and phrases like "go to work" or "open washio" work too. Other command words: `outline` (item 12), `help` / `secrets` (opens the item 0 sheet), `layers` (item 17), `present` (item 26), `history` (item 30), `dev` / `inspect` (item 28). Esc cancels.

**How:**
- A fixed `.cchat` div with an `<input>` is positioned at the pointer, +16px/+18px and clamped to the viewport, and it follows `pointermove`.
- The `/` keydown calls `preventDefault` (Firefox's quick-find uses `/`) and focuses the input synchronously. An earlier version focused on a timeout and lost the first typed characters.
- The input's keydown calls `stopPropagation` so site shortcuts don't fire while typing.
- Navigation sets `location.hash` after 450ms so the bubble is seen first.
- Command extension: before making a bubble it calls `window.hgCmd(word)` if that exists, and later rounds wrap `hgCmd` to add words.

**Desktop only.**

### 2. Red pen on page titles (Edit any big title)
**What:** Hovering any `main h1.big` shows an "Edit" chip above its right edge. Clicking it makes the title editable, with a red dashed outline and a red caret. As the visitor types, a card under the title shows handwritten notes in red, like an editor would:
- "No dashes. Use a full stop, it reads calmer."
- "'leverage' is a buzzword. Say what it actually does." (about 30 buzzwords are watched)
- "One exclamation mark is already shouting."
- "That's a paragraph now, not a title." (over 80 characters)
- "A title needs a few more words than that."
- Otherwise, in green: "Reads fine. I'd ship that."

The title gets a red wavy underline while problems remain. Enter or "Keep it" keeps the edit. Esc or "Put it back" restores the original. After an edit, the chip turns into "Put it back".

**How:**
- `contentEditable="plaintext-only"`, falling back to `"true"`.
- The original `innerHTML` is kept in a `WeakMap`. The site had split titles into per-letter `.cx` spans with `SPLITCH`; editing flattens them to plain text, and restoring puts the spans back.
- A capture-phase keydown on the title calls `stopPropagation` so the hero pen shortcuts (`p`, `x`) and the easter egg don't fire mid-edit.
- The notes are regex checks run on `input`.

**Depends on:** the site's `h1.big` titles. **Desktop only.**

### 3. The Work title edits itself (tracked changes)
**What:** The first time the Work page opens in a session, its title plays out as a tracked change:
1. It shows "Apps I made."
2. "made" gets a red strike and a comment pill appears under the title: "(H) 'made' is vague. Say which part."
3. "made" collapses and "designed and built" types in letter by letter, in blue with an underline and a red caret.
4. The comment turns into "✓ Resolved" and fades, and the real title returns.

**How:**
- A step queue of `setTimeout`s that swaps the h1's `innerHTML` for `Apps I <span.tc-d>made</span><span.tc-i></span><span.tc-caret>` and restores the original `innerHTML` at the end.
- It only runs if the title text is exactly "Apps I designed and built." so a copy change won't break it.
- If the visitor leaves mid-animation it restores immediately.
- It runs once per page load and is skipped under reduced motion.

### 4. Click the words to change what Hardik designs (hero)
**What:** In the home hero line "I design **apps & websites** end to end, then build them in code", the bold part becomes a dashed-underlined button. Each click rolls it to the next option: Android apps, home screen widgets, design systems, micro-interactions, scroll systems, then back to apps & websites. The old words slide up and out, the new ones slide in, and the button's width animates between them.

**How:**
- A `<button class="wslot">` holding one `<span>`, with `display:inline-grid`, both spans in `grid-area:1/1`, and `justify-self:center`. That last part is required, because otherwise grid items stretch and the width is measured wrong.
- Width stays `auto` except during the animation: set it to the old width in px, force a reflow, set the new width, then clear it after 460ms. Setting it on load broke on phones when the page loaded on another route.
- The button needs `letter-spacing:inherit` because the claim uses tight tracking.
- After each change it fires a `resize` event so the site's `placeNeg()` re-copies the claim into the hero's inverted "negative" overlay (`#nClaim`).

### 5. Nav labels decode on hover
**What:** Hovering a nav label (Home, Work, …), a footer nav link or a case study link button scrambles the text through pixel glyphs (`▚▞▙▟▛▜░▒▓#%&*+=/<>`) and resolves it left to right in about 0.4s.

**How:** A `setTimeout` loop at 32ms per frame. The element's width is locked with `min-width` during the scramble so the nav doesn't jitter. It's delegated on `pointerover`. **Desktop only**, and skipped under reduced motion.

### 6. Redacted words you scrub clean (About)
**What:** On the About page's education line, the words "which is either a red flag or the most interesting thing about me" start blacked out with bars, with a handwritten hint "scrub the black bits". Moving the pointer, or dragging a finger, across a word peels its bar away. When all are clear, the hint changes to "well, now you know".

**How:**
- Each word is wrapped in `.rdw`, whose `::before` is an ink bar that animates `scale:0 1` when `.clr` is added.
- `pointermove` and `pointerdown` handle mouse. On touch, `touchmove` uses `document.elementFromPoint`.
- The whole phrase keeps an `aria-label`, so screen readers read it normally.

**Copy note:** while in there I also removed an em dash from that line ("That's the whole list. The design is self-taught…").

### 7. Tone slider: Plain to Corporate (About)
**What:** Under the About title there's a "Plain ↔ Corporate" range slider with four steps. Each step rewrites the title word by word, with each word blurring in on a stagger:
1. The original: "I came out of a commerce degree and ended up designing products."
2. "I came from a commerce background and moved into designing products."
3. "Leveraging a commerce background, I transitioned into crafting user-centric solutions."
4. "Leveraging a finance first mindset, I pivoted to delivering best-in-class, seamless digital experiences at scale."

Buzzwords get a red wavy underline. A handwritten note under the slider changes with each step: "This is how I write." (green), "Still fine. A little stiff.", "Now it sounds like a LinkedIn post.", "I won't write this. Slide it back, please."

**How:** Buzzwords are marked with `*asterisks*` in the source strings and turned into `span.tw.bz`. Step 0 restores the original `innerHTML`. Moving the slider also calls `hgPenReset` so it doesn't fight item 2.

**Flag:** This deliberately puts corporate buzzword copy on the page, as a parody that says Hardik refuses to write it. It bends the "no buzzwords" rule on purpose. Let Hardik decide.

### 8. Selection toolbar: highlight, copy, read aloud
**What:** Selecting any text in the page shows a small dark toolbar above the selection with four pillar-colour swatches (highlight), "Copy", "Read it" (text to speech) and a word count ("12 words"). Copy shows the toast "Copied. Take it, no credit needed."

**How:**
- On `mouseup`, and on `keyup` with Shift, it reads `getSelection()`.
- It ignores selections inside inputs, editables, its own UI and the canvas page.
- Highlights use the CSS Custom Highlight API (`CSS.highlights`, `new Highlight()`, styled with `::highlight(hg-y)` etc.). This works across element boundaries and never touches the DOM. Where the API is missing it falls back to `range.surroundContents(<mark>)`, and says so if the selection spans blocks.
- "Read it" uses `speechSynthesis`, and the button is hidden if that's unsupported.
- The toolbar's `mousedown` calls `preventDefault` so clicking it doesn't clear the selection.

**Desktop only.**

### 9. Reading ink (not in the counter)
**What:** In case study pull quotes (`.cs-quote`) and the About page's beat paragraphs (`.abeat p`), each word starts at 22% opacity and darkens to full as you scroll through the paragraph, like reading with a pen.

**How:**
- Text nodes are split into `span.iw` words, keeping any inline markup.
- On scroll (rAF, passive), progress = (0.88 × viewport height − paragraph top) ÷ (paragraph height + 0.4 × viewport height), and the first N words get `.on`. The DOM is only touched when N changes.
- It re-scans on `hashchange`, since case bodies are re-rendered.

**Don't** point it at `#storyText`: that has its own word system.

### 10. Big titles lean toward the cursor
**What:** Moving the pointer across any big page title makes the letters within about 150px lift up to 10px, grow up to 18% and lean slightly toward the cursor. The closest ones turn accent blue, and they spring back as the pointer moves on.

**How:**
- It uses the site's existing per-letter `.cx` spans (from `SPLITCH`). Each letter's centre is measured once when the pointer enters a title, then only `transform` and `color` are written in rAF, so there are no layout reads per frame.
- The site's reveal animation already uses the separate `translate` property. So this uses `transform`, and the CSS extends the existing `.chs.in .cx` transition list rather than replacing it; replacing it would break the reveal.
- It resets on scroll and skips a title while it's being edited (item 2).

**Desktop only.**

### 11. Knock the footer over
**What:** Clicking the footer's last line, "Built in code, not a builder.", makes every letter fall with gravity, tumbling and bouncing onto the bottom of the screen and against the side walls. Clicking again springs them all back into place with a bouncy stagger. It works from the keyboard too (Enter or Space).

**How:**
- Each character is wrapped in `span.fl`. A small rAF physics loop runs per letter (gravity 0.55, 38% bounce, friction, spin) and writes `transform` only.
- The floor is 40px above the viewport bottom so letters don't vanish under the site's bottom blur (`.eblur`).
- The return is a CSS transition.
- It resets on route change and is skipped under reduced motion.

### 12. Outline mode (`Shift + O`)
**What:** Figma-style outline view of the whole page. Every element in `main` and the footer gets a thin blue outline, shadows go away, images, videos, canvases and SVGs fade to 18%, and a faint blue baseline grid appears behind. A blue tag at the top reads "Outline mode · Shift O to leave".

**How:** A single `body.olm` class plus CSS. There's no JS beyond the toggle, so it's cheap because it's paint only.

### 13. The tab title notices you left
**What:** When the visitor switches tabs, the title becomes one of "Come back, the chai is getting cold", "Still here. Take your time" or "The doodles are waiting for you". Coming back after more than 1.5s shows "Oh hi, you're back" for 1.8s, then the real title returns.

**How:** A `visibilitychange` listener. It saves `document.title` when the tab is hidden and restores it after.

### 14. "Say my name" (Hardik's existing easter egg, only hooked in)
**What:** Typing `hardik` anywhere already made the doodles say hi. Round 19 only added a listener so it counts in item 0. Nothing to build.

---

## Round 20: things a website isn't supposed to do (items 15 to 23)

### 15. Two windows, one site
**What:** The one most likely to make people's jaws drop. Opening the site in two browser windows side by side makes a thick ink rope with flowing dashes in pillar colours hang from the centre of one window to the centre of the other. It crosses the real physical gap between the windows, and each window draws only the part inside itself, so it looks like one rope. Each end has a coloured orb with a handwritten label, "this window" or "the other one". Moving a window makes the rope stretch and re-sag live, with a gentle sway. The visitor's cursor also appears as a ghost arrow tagged "YOU, NEXT DOOR" inside the other window.

**How:**
- A `BroadcastChannel("hg-twin")` carries positions. Every 50ms each window posts its screen position (`screenX + (outerWidth - innerWidth)/2` and `screenY + (outerHeight - innerHeight)`, which estimates where the page content starts), its size and the cursor's screen position, but only when these changed or 700ms have passed.
- A full-screen `<canvas>` with `pointer-events:none` draws, in each window's local coordinates, the same screen-space quadratic curve between the two centres.
- The curve's sag and sway are computed from the screen-space endpoints and `Date.now()`, and the endpoints are sorted by window id. That's what makes both windows draw the identical curve and dash offset.
- The rAF loop only runs while a peer has been heard from within 2.5s, so it costs nothing normally. `pagehide` sends a "bye".

**Desktop only.** In headless testing every window sits at 0,0, so a second window had to be faked with an overridden `screenX`. Check it on real hardware.

### 16. Hold Alt: the page measures itself
**What:** While Alt (Option on a Mac) is held, whatever is under the cursor gets a Figma-blue box with a "W × H" label, its parent gets a dashed outline, and red Figma redlines with px labels show the real distance from each side of the element to its parent.

**How:**
- `elementFromPoint` gives the hovered element (SVG children resolve to their `<svg>`).
- The "parent" is the nearest ancestor whose box is actually a different size, which skips wrappers of the same size.
- The overlay is a fixed div rebuilt in rAF on `pointermove` or scroll while Alt is held.
- Alt's keydown calls `preventDefault`, which stops the Windows menu-bar focus, and the overlay is hidden on key release or window blur.

**Desktop only.**

### 17. Press L: a live layers panel
**What:** A Figma-style Layers panel on the left for the current page. It shows the real DOM tree, with rows indented by depth and icons: `#` frames in blue, `T` text, `▣` images, `✦` vectors, `◉` links and buttons. Names come from the element's id, its first class, or the text itself ("Section · page-head", "T I came out of a commerce degre…", "Button · Start a project"). Hovering a row outlines that element on the page with its name and size. Clicking scrolls to it and keeps it selected. The eye icon hides the element for real, and all hidden layers come back when the panel closes. The footer note reads "This is the real page, not a picture of it."

**How:**
- A recursive walk of `.view.on`, up to depth 6 and 420 rows. It skips invisible elements and the site's own overlays, and doesn't go inside `p`, `h1`–`h6`, `a`, `button`, `img`, `video` or `svg`.
- The panel is rebuilt on route change while open.
- Hiding sets `style.visibility`.

**Desktop only.**

### 18. Hold C: a code lens
**What:** While C is held, a 280px round dark lens with a thick ink ring follows the cursor. Inside it is the real HTML of the element underneath, syntax coloured (tags blue, attributes purple, strings green) with base64 data cut to `data:image/png;base64,…`. A yellow label reads "the code under your cursor", and the inspected element gets a yellow dashed outline.

**How:**
- The lens is `pointer-events:none`, and it's hidden for a moment during `elementFromPoint` so it doesn't find itself.
- It climbs from tiny elements (single letters) to a parent until the `outerHTML` is at least 160 characters.
- The colouring is a single-pass tokenizer over the raw HTML that escapes each piece. An earlier chain of regex replaces re-coloured its own output, so don't do it that way.
- The HTML is truncated to 1100 characters, with newlines inserted between tags.

**Desktop only.**

### 19. Favicon minimap (not in the counter)
**What:** The browser tab icon is redrawn as a tiny map of the current page: a rounded paper tile with one pillar-coloured bar per section, and an ink rectangle showing where the viewport is. It moves as you scroll.

**How:** A 64×64 canvas. Top-level children of `.view.on` over 40px tall are mapped to bars by their document position. The result is set with `link[rel=icon].href = canvas.toDataURL()`, throttled to 180ms, and only redrawn when the rounded position or route changes. It creates the `<link rel="icon">` if none exists.

### 20. Slingshot back to the top
**What:** At the very bottom of any page, keep scrolling down, or keep pulling on a phone. A rubber band appears between two posts with a yellow "back to top" ball, and it stretches further the more you pull, with a handwritten "keep pulling" that turns into "let go". Release past the threshold and the ball fires up off the screen while the page scrolls smoothly to the top. Release early and the band springs back.

**How:**
- On `wheel`, when at the bottom (`scrollY + innerHeight >= scrollHeight - 4`), tension builds by `deltaY / 1100`. On touch, it's the drag distance ÷ 360.
- Release is detected as 170ms with no wheel event, or `touchend`.
- The band is one SVG quadratic path. The spring-back is a small damped rAF loop, and the fling is a CSS transition on the ball.
- The threshold is a tension of 0.55.
- Skipped under reduced motion.

### 21. Resize ruler with real breakpoints
**What:** While the window is being resized, a px ruler appears across the top (ticks every 10px, labels every 100px), with dashed red lines at the site's real breakpoints: "560 px · phone layout", "760 px · hero doodles stop arranging", "820 px · stool cards go compact", "1000 px · case study grids fold". A dark readout bottom right shows the live width. Crossing a breakpoint shows "crossed 820 px: stool cards go compact" for 1.6s. It fades 1.1s after resizing stops.

**How:** A plain `resize` listener rebuilds a small overlay. Each breakpoint's line sits at x = its px value, so the window's own edge approaches it as you shrink. Those breakpoint values and descriptions come from the site's media queries and `measure()` / `curate()`. Re-check them if the layout changes.

### 22. Printing gives a one page CV
**What:** Cmd or Ctrl + P on any page prints a clean one-page CV instead of a broken screenshot of the site:
- "HARDIK GUPTA" in the pixel font.
- The role line, email and site host.
- "Selected work", one entry per project from `P`, each with a pillar-colour square, name, type and gist.
- "Experience", pulled live from the About timeline, with year ranges written as "2024 to 25" and "2026 onwards" and dashes turned into colons.
- A handwritten footer: "Printed from the live site. The real one moves a lot more than this page does."

**How:** A hidden `.pcv` div, rebuilt on `beforeprint`. `@media print` hides every other child of `body` and shows `.pcv`, with `print-color-adjust:exact` so the colour squares print.

**Flag:** It includes the Flipkart line because it mirrors the About timeline. It's not given any emphasis, but Hardik may want it left out of print.

### 23. Sleepy doodles
**What:** After 30 seconds with no pointer, key, scroll or touch activity, every on-screen doodle gets a floating "z z z" bubble, staggered 400ms apart. The first movement after that replaces them with startled bubbles ("oh!", "huh?", "i'm up", "!!") that pop away.

**How:** An idle timer reset by activity events. It reuses the site's existing `.hey` speech-bubble style (the one from the "hardik" easter egg) and adds a looping bob animation, `.hey.zz`. It only targets `.view.on .doo` that are actually on screen.

---

## Round 21: tricks that also sell the work (items 24 to 31)

### 24. Copy a doodle, paste it into Figma
**What:** Hovering any doodle (or double-clicking it to select it) and pressing Cmd or Ctrl + C puts the doodle on the clipboard as clean standalone SVG. Pasted into Figma it becomes real editable vector layers. Toast: "Copied as SVG. Paste it into Figma, it's real vectors."

**How:**
- The doodles are `<svg class="dd">` elements that `<use href="#dd-laptop">` shared `<symbol>`s from a hidden sprite, so a naive copy would be empty outside the page.
- The export clones the SVG, and replaces each `<use>` with a `<g transform="translate(x y) scale(w/vbW h/vbH) translate(-vbX -vbY)">` holding the symbol's inner markup, filled with the live `<use>`'s computed fill.
- It then strips `class`, `style` and SMIL animation tags, adds `xmlns`, and sets width and height to a quarter of the viewBox.
- It's written with `navigator.clipboard.writeText` in the keydown (which counts as a user gesture). It only fires when there's no text selection, so normal copying is untouched.
- Verified by rendering the copied file on its own: it draws correctly.

**Desktop only.**

### 25. Drag a work card onto the desktop
**What:** On the Work page, dragging an app card out of the browser and dropping it on the desktop creates a real image file of that app's screen (for example `alter-app-screen.webp`).

**How:**
- Chrome's `DataTransfer.setData("DownloadURL", "mime:filename:url")` on `dragstart`.
- The card's cover `<img>` data URI is turned into a `Blob` and `URL.createObjectURL`, and that's done on `pointerdown` so it's ready synchronously when the drag starts. For a video cover (Washio), the current frame is drawn to a canvas and saved as PNG.
- It's cached per card, and the link still works normally for clicks.

**Chrome/Edge desktop only.** Other browsers just drag the link as usual.

### 26. Present mode (P, or the "Present this" button)
**What:** Every case study gets a "Present this [P]" pill under its title. Pressing it, or P, turns the case study into a fullscreen deck:
- A title slide: pillar-colour tag, huge project name, gist, and "Hardik Gupta" in handwriting.
- One slide per case study section.
- The CTA.
- A closing slide: "Questions?" and the email.

Arrow keys, Space, PageUp/PageDown, Home/End and swipe move between slides. There's a counter ("4 / 14"), a progress bar in the project's pillar colour, and Done or Esc to leave. It uses the browser's real fullscreen, and leaving fullscreen closes the deck.

**How:**
- Slides are `cloneNode(true)` copies of `#caseBody .cs-sec` (ids stripped so they don't clash with the live page), or `.step` blocks on the thin fallback pages.
- Each slide is scaled down to fit the height, but never below 62%; below that it scrolls.
- A capture-phase keydown on `document` calls `stopPropagation` while the deck is open, so no other shortcut fires underneath.
- The button is re-added by a `MutationObserver` on `#caseHead`, since `renderCase` rewrites it on every route.
- The found pill and the custom cursor are hidden while presenting.

**Flag:** it's only as good as the case study. On Parchi and Hourbit, which still use the stub pages, the deck is thin.

### 27. Live Parchi demo: say the order, the bill writes itself
**What:** On the Parchi case study, a yellow card holds a live demo. On the left:
- The title "Say the order. Watch the bill write itself."
- A हिंदी / English language toggle.
- A big "Tap and say the order" mic button.
- A text box with "Add", for people without a mic.
- Example chips: "do kilo chawal aur ek maggi", "2 kg atta, 1 litre tel and 6 eggs", "आधा किलो चीनी और दो पैकेट दूध".

On the right is a receipt-style bill ("PARCHI", today's date, "counter 1"). While the visitor speaks, items appear live as faded rows, then become real rows when the phrase finishes. Rows have − and + buttons, there's a running total, "Clear", and "Share bill" (the native share sheet on phones, copy to clipboard on desktop, with the toast "Bill copied. Paste it in WhatsApp.").

**How:**
- The Web Speech API (`SpeechRecognition` / `webkitSpeechRecognition`), `lang` `hi-IN` or `en-IN`, `continuous` and `interimResults`. Interim results render as the faded ghost rows. Final results are added to the bill, and repeated items merge.
- The parser is token based:
  - Number words in Hinglish, English and Devanagari, including aadha/half 0.5, dedh 1.5, dhai 2.5 and paav 0.25, and Devanagari digits.
  - Unit words: kilo/kg/किलो, gram/g/ग्राम (converted to kg), litre/l/लीटर, ml, packet/pkt/पैकेट, piece, bottle, dozen/दर्जन (×12).
  - A catalogue of 20 kirana items with Hinglish, English and Devanagari aliases, including two-word ones like "chai patti".
  - `aur` / `and` / `और` / `फिर` / commas end a clause, so a quantity said after the item ("chai patti do packet") attaches correctly.
- Tested orders all come out right: "do kilo chawal aur ek maggi", "2 kg atta, 1 litre tel and 6 eggs", "आधा किलो चीनी और दो पैकेट दूध", "500 gram dal ek dozen ande", "chai patti do packet aur ek bread", "teen maggi paanch biscuit".
- Browsers without speech (Firefox) get a disabled mic reading "Voice needs Chrome or Edge. Type below". A blocked mic gets "The mic is blocked. Allow it in the address bar, or type below."
- Speech recognition needs HTTPS (fine on Vercel).
- It's inserted into `#caseBody` only when the route is `#/work/parchi`, via a `MutationObserver` plus `hashchange`.

**Flags:**
- The demo says on the page "A small rebuild of Parchi's core idea for this page, not the app's own code. The rates are sample numbers." CLAUDE.md wants live demos ported from the real source, so swap the parser and catalogue for Parchi's real ones when Hardik provides them.
- It shows ₹ amounts on sample rates. If "no prices anywhere" is meant to cover this too, show quantities only.

### 28. Dev Mode (press D)
**What:** Like Figma's Dev Mode. A green tag at the top reads "Dev Mode · click anything · D to leave", and the cursor turns into crosshairs. Hovering outlines elements in green with "tag.class W × H". Clicking any element opens a panel on the right with:
- **Layout:** width, height, display, gap, padding.
- **Typography:** font, weight, size, line height, letter spacing, colour swatch and hex. Shown for text elements.
- **Appearance:** fill hex, gradient or image, stroke, radius, shadow layers, opacity.
- **CSS:** a dark code block with the generated CSS, and a green "Copy CSS" button.

Links and buttons don't fire while it's on, so clicking always inspects.

**How:**
- All values are real, from `getComputedStyle` and `getBoundingClientRect`, with rgb/rgba converted to `#HEX` and an opacity %.
- Capture-phase `pointerdown`, `mousedown`, `click` and `dragstart` listeners call `preventDefault` and `stopPropagation` everywhere except the site's own new UI.
- Ignored on `#/canvas` (which has its own editor keys) and while presenting. Esc or route change turns it off.

**Desktop only.**

### 29. Address bar reading progress (not in the counter)
**What:** As the visitor scrolls, the URL shows a little track: `…/?you-are-here=---------o---------#/about`. The `o` moves along 18 dashes, and the query disappears again at the top of the page.

**How:**
- `history.replaceState(history.state, "", pathname + query + hash)`. It only changes the query string (`?`), never the hash, so the hash router and `hashchange` are untouched.
- The track is ASCII only, because other characters get percent-encoded in some address bars, and `#` can't be used in a query.
- It's rate-limited to one update per 350ms and only runs when the step changes, because Safari throws if `replaceState` is called more than 100 times in 30 seconds.

### 30. Version history of your visit (`Shift + H`)
**What:** A Figma-style "Version history" panel on the right, subtitled "Your visit, saved like a Figma file. Click any point to go back to it." It shows a vertical timeline, newest first:
- "Current version"
- Entries like "Opened Washio case study", "Read 'The calls that mattered.'" and "Found: Slingshot"

Each entry has a coloured dot and a relative time ("just now", "2 min ago"). Clicking an entry takes the visitor back to that page and scroll position. The footer reads "Only in this tab. Nothing leaves your browser."

**How:**
- Entries are `{time, title, hash, scrollY, kind}` in `sessionStorage["hg-hist"]`, capped at 80.
- They're logged on `hashchange` (page opened), and after 900ms of scroll idle if the visitor moved more than 60% of the screen. Those are labelled with the last `h1` or `h2` above mid-screen, and consecutive ones within 15s merge.
- Secrets found come from the `hgfound` event that item 0 fires.
- Jumping sets the hash if needed, then smooth-scrolls after 700ms.

**Desktop only** (keyboard). The cursor chat word `history` opens it too.

### 31. A note for whoever opens DevTools
**What:** Opening the browser console shows a big yellow "HARDIK" badge in the pixel font, then "You opened DevTools on a portfolio. Good instinct. This whole site is one hand written HTML file. No framework, no build step, nothing to install." It then lists three working commands:
- `hardik.hire()` opens an email to hardik10.g@gmail.com with the subject "Found you in the console".
- `hardik.secrets()` opens the item 0 list.
- `hardik.source()` prints a table: page size in MB, how many images, videos and fonts are embedded, Frameworks 0, Build steps 0. It returns "One file. That's the whole site."

**How:** `console.log` with `%c` CSS styling (Chrome, Firefox and Safari all support it), and a global `window.hardik` object with those three methods plus a `toString` hint.

---

## Suggested order if rebuilding from scratch

1. Build **0** first, since everything calls `found()`.
2. Then the cheap, self-contained ones: **12, 13, 19, 21, 22, 29, 31**.
3. Then the ones that touch site internals, which need the most care: **2** (SPLITCH and `h1.big`), **4** (`placeNeg`), **10** (`.cx` transitions), **26** and **27** (`#caseHead` / `#caseBody` re-renders), **23** (the `.hey` style).
4. After each one, run the regression pass from CLAUDE.md: every hash route at 1440px and 390px, no horizontal overflow, no `pageerror`.

## Open decisions for Hardik

- **7, tone slider:** keep a deliberate buzzword parody or not.
- **27, Parchi demo:** replace the approximated parser and catalogue with the real app's logic, and decide on showing ₹ amounts.
- **22, print CV:** include or leave out the Flipkart timeline entry.
- **26, present mode:** the Parchi and Hourbit decks stay thin until their full case studies exist.
