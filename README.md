# Stardoku website

Three static pages. No build step, no framework, no dependencies. Upload the
folder anywhere that serves files.

```
index.html        Home
printables.html   Free puzzle generator + print sheets
privacy.html      Privacy policy (the URL App Store Connect requires)
assets/           Screenshots, taken from the real game
```

## Hosting

**GitHub Pages** is free and fine: create a repo, upload these files, then
Settings → Pages → deploy from `main` / root. Works with Netlify, Cloudflare
Pages or any normal web host too.

Your privacy policy URL will then be `https://<your-site>/privacy.html`. Put
that in App Store Connect.

## Before you publish

1. **Set the App Store link.** In `index.html`, find `id="appstore"` and replace
   `href="#"` with your real App Store URL.
2. That is the only placeholder. The contact address
   (`stardokulogicpuzzle@gmail.com`) is already wired into the footer, the
   contact section and the privacy policy.

## The printables generator

Real puzzles, generated in the visitor's browser by the same engine and verified
by the same solver the app uses. Nothing is precomputed and nothing is fetched.
Every puzzle has exactly one solution and is solvable by deduction alone —
spot-checked here, 20 out of 20 unique, logic-solvable and with connected
regions.

Impossible size/difficulty combinations are disabled with an explanation rather
than left to fail: a 10×10 cannot be built that needs only the easiest ideas,
because more room always forces a harder move somewhere.

Searching runs in slices across animation frames with a hard time budget, so a
slow combination shows progress instead of freezing the tab.

**On paper**, regions are drawn as thick outlines rather than filled colour.
That is the convention for this family of puzzle, it survives photocopying, it
stays readable in black and white, and it does not drink a cartridge of ink.
Region letters are an option for anyone who is colourblind or working in pencil.

## Accessibility and performance

- Total page weight around 400 KB including all imagery
- Screenshots are progressive JPEG and lazy-loaded below the fold
- Every animation is disabled under `prefers-reduced-motion`
- Skip link, semantic landmarks, labelled controls, visible focus
- No horizontal scroll at 390 px; checked at 390, 768 and 1280

## Fixes in this revision

**Phone mockup / images appearing blank.** The `.phone img` (hero mockup) had
no sizing CSS at all — only the smaller `.shot img` thumbnails had
`width:100%;display:block`. Fixed by adding an explicit rule with
`object-fit:cover`. If images still look wrong on your specific host, open
devtools → Network and check for 404s on `assets/*.jpg`; a themed placeholder
(a gold star) now shows automatically instead of a blank box if any image
fails to load, so it's obvious at a glance if something didn't upload.

**Print button.** Was the same faint "ghost" style as every other secondary
button, sitting inline with "Generate puzzles" — easy to lose. It's now a
large gold button with a printer icon, on its own row below a divider, with a
caption explaining it opens the browser's print dialog (choose "Save as PDF"
there for a file instead of printing immediately).

**Reviews section.** Added at `index.html#reviews`, linked from the nav. Has a
"Rate on the App Store" button (placeholder `href="#"` — set your real link
alongside the one in the hero) and a lightweight form that opens a pre-filled
email to stardokulogicpuzzle@gmail.com — no backend needed.

**Scroll/reveal timing.** Loading a page directly at a `#hash` (or a nav link
pointing at one) used to smooth-scroll and fade in on arrival, which for a
long page could take over a second and look broken mid-transit. Hash
navigation on load now jumps instantly and reveals the target section
immediately; smooth-scroll + fade-in still happens for normal in-page
scrolling.

**Two escaped-character bugs.** A couple of apostrophes and em dashes were
written as literal `\'` / `\u2014` in HTML text (those escapes only work inside
`<script>`, not in page text) and were rendering as visible backslashes.
Fixed with proper HTML entities.

## Images fixed (self-contained now)

All screenshots on the home page are now **embedded directly in `index.html`
as base64 data URIs** — there is no `assets/` folder anymore and nothing to
upload separately. This was a real bug: the images were 404ing on the live
site (confirmed from a screenshot of the broken page — my `onerror` fallback,
a lone gold star, was firing on every missing image, both the hero phone and
the five screenshot thumbnails).

The files opened fine locally, so the cause was deployment: the `assets/`
folder wasn't being served alongside `index.html`. **If you are hosting on
Google Sites, this is expected** — it doesn't serve an arbitrary folder of
files next to your HTML the way a normal static host does, so relative image
paths like `assets/hero.jpg` never resolve there.

Inlining as base64 removes the problem at the root: `index.html` is now one
file with zero external dependencies (confirmed: zero network requests fire
when the page loads). Wherever you paste or upload it, the images travel with
it. The trade-off is file size — `index.html` is now ~480 KB instead of ~30 KB
plus a separate 330 KB of images — which is still light for a modern page.

`printables.html` and `privacy.html` were never affected; they don't reference
any image files.

## Interactive hero + printables upgrade

**The hero is now a playable puzzle.** Instead of a static screenshot, visitors
get a real 5×5 Stardoku generated in their browser by the same engine the app
uses — tap once for ×, twice for a star. It validates live against the real
rules (one star per row, column and colour; none touching, including
diagonally), shakes and flags conflicting stars, and on a correct solve draws
the constellation between your stars before showing a "Solved" card. "New
puzzle" generates a fresh one. Difficulty is capped to levels 1–3 so the
homepage puzzle stays winnable in a minute or two.

The gentle float animation is disabled on touch devices — a tap target that
never stops moving is genuinely hard to hit, and it was measurably
un-tappable in testing.

**Printables now have three region styles**, selectable before generating:

- **Outlines** — thick black borders only. Least ink, photocopies perfectly.
- **Colour + outlines** (default) — pale tints plus strong borders. Easiest to
  read, still prints cleanly in colour or greyscale.
- **Colour** — tints with lighter borders, closest to the app.

Tints are deliberately pale so they stay legible in colour print *and* reduce
to distinguishable greys on a mono printer. `print-color-adjust:exact` is set
so browsers don't strip the fills when printing.

**Include answers is now off by default.**

**App nod added** below the generator, with a QR placeholder box sized and
styled ready to drop a real code into once the App Store link exists. Every
printed sheet also carries a small footer credit line.

**Brand subtext** "The Stellar Logic Puzzle" now sits under the STARDOKU
wordmark in the header and footer of all three pages (hidden below 420px where
it would crowd the menu button).

## Animated star hero + Daily Star page

**The hero is now a pure animated star.** The playable mini-board was removed —
it was janky, and a live puzzle competing with the headline was the wrong first
impression. In its place: a gold star with a soft pulsing glow, three
counter-rotating orbit rings carrying small satellite bodies, and twinkling
sparks. All CSS, no JavaScript, and fully disabled under
`prefers-reduced-motion`.

**`daily.html` is new** — the Daily Star, matching the game:

- **9×9, always difficulty 3+**, exactly like the app's Daily
- **Seeded from the local calendar date**, so everyone worldwide gets the same
  board and it rolls over at local midnight (not UTC — same as the app)
- Same rules and validation: one star per row, column and colour region; none
  touching, including diagonally. Clashing stars shake and are flagged
- Live timer, and a countdown to tomorrow's puzzle
- **Progress saves to this browser**, so you can leave and come back
- On a correct solve it draws the constellation between your stars, then shows
  a "Solved" card with your time

Verified: deterministic across fresh sessions, progress survives a reload, and
a full 9-star solve triggers the constellation and win state correctly.

**Daily is now the primary call to action** on the home page — a large gold
button above the App Store link — and is in the nav and footer of every page.

## Responsive fixes found while testing

Three genuine layout bugs, all caught by sweeping every page at 320/360/390/
414/768/1024/1280/1600px:

- **The screenshot carousel leaked horizontal scroll to the whole page.** It's
  meant to scroll sideways on its own; it now has `overscroll-behavior-x:
  contain` and `max-width:100vw`.
- **The brand subtext forced the header 4px wider than the viewport at 768px.**
  It's decorative, so it's now absolutely positioned in the nav and excluded
  from width calculation, and wraps freely in the footer.
- **`minmax(320px,1fr)` grids overflowed at 320px viewports** — a 320px minimum
  cannot fit inside a 276px column. Now `minmax(min(320px,100%),1fr)`.

All four pages now pass with zero overflow and zero console errors at every
tested width.

## Daily gameplay corrected to match the app

The first version of the Daily let you place a star on any square and only
flagged rule conflicts. That was wrong. It now mirrors the app exactly:

- **Drag to sweep X's on and off.** Dragging paints X's; starting a drag on an
  existing X wipes them off instead. Stars are never disturbed by a sweep.
- **Double-tap to place a star.**
- **Stars are validated against the real solution**, not just against rule
  conflicts. You can no longer build a board of stars that merely happens not
  to clash.
- **Three hearts.** A wrong star costs a heart, turns into an X, and shakes with
  a red flash — same as the app. At zero hearts the puzzle ends.
- Mistakes are counted and reported on the win card.

Verified end to end: a correct star is accepted with no heart lost; three wrong
stars drain the hearts and trigger the lose state; dragging marks and unmarks
X's without touching hearts; a full 9-star solve draws the constellation and
reports the time and mistake count. Checked on both desktop pointer and mobile
touch.

The "Check" button was removed — validation is immediate now, as in the app, so
it had nothing to do.
