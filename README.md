# CSS Stylist

A single-file **CSS editor for beginners**: Nyla's HTML portfolio is finished
but unstyled — students fill in `style.css` until the live preview matches the
reference design, ticking off the quest list as they go. It is the CSS
companion to [nyjc-computing/html-doctor](https://github.com/nyjc-computing/html-doctor):
same editor (line numbers, syntax colouring, wavy underlines, plain-English
findings), but the stylesheet is the editable file and the page is fixed.

- **The page is Nyla's html-doctor portfolio, bug-free.** The *index.html* tab
  shows the same page as the HTML Doctor lesson — every planted bug fixed,
  with `class`/`id` styling handles added (`section`, `highlight`, `motto`,
  `#top`, `#footer`, …). It is read-only: styling problems cannot be fixed in
  the markup, only through CSS.
- **The stylesheet starts as a bare skeleton.** Every rule the page needs is
  pre-written with an empty body — comments are left out entirely (they tend
  to confuse beginners). The first quest item's rule is set up as a fill-in
  placeholder (`body { background-color: }`): students supply the value the
  quest item names, then apply the same `property: value;` shape to the
  remaining empty rules. The empty placeholder deliberately produces the
  app's first finding ("'background-color' has no value"), demonstrating the
  findings pane immediately. `?example=starter` loads a smaller two-rule
  example instead.
- **Quest tracker.** 28 items in the spirit of html-inspector's RefG hunt,
  one per declaration the page needs, phrased as an instruction — the exact
  colour or measurement to use is given in monospace inside parentheses
  ("Set the title bar background to navy `(#16324c)`", "Pad the title bar
  `(28px 16px)`"), but the property to use is not: choosing it is the
  exercise. Items are checked against a hidden
  same-origin copy of the page with `getComputedStyle`: an item ticks only
  when the browser really applies that exact declaration (a near-miss shade
  or a missing unit stays unticked). Progress bar
  and collapse state persist; the tracker completes at 28/28.
- **A reset button for worst cases.** The draft autosaves to the browser, so
  a student who mangles their stylesheet irrecoverably needs a way back:
  **Reset** (top right) restores the original skeleton after an explicit
  confirm `<dialog>` ("Keep my stylesheet" / "Reset" — no `window.confirm`,
  which the campus embed sandbox blocks). Only the draft is reset; quest
  progress restarts with it (it is measured from the stylesheet), while the
  reference mode and layout choices are preferences, not work.
- **Reference behind a floating button.** Hovering the **Reference ▣** button
  (top right of the preview) peeks at a screenshot of one possible finished
  look; clicking pins it open until clicked again. It is an **image, not a
  stylesheet**: the target values exist only in the teacher's answer key,
  not in the app source.
- **CSS error linting as you type** — missing `:` and `;`, missing/stray
  `{}`, declarations outside a rule (including pasted HTML), unknown
  properties ("*colour* — did you mean *color*?"), invalid values (missing
  units, wrong colours, bad font stacks), each with a plain-English
  explanation, a fix, and an MDN link. Findings appear as wavy
  red/orange/blue underlines and in the bottom pane, exactly like HTML Doctor.
- **Selector checks against the real page.** The app knows the fixed HTML, so
  a typo'd `.sectoin` gets "No element has the class *sectoin* — did you mean
  `.section`?"; `footer { }` gets "there is no `<footer>` element — *footer*
  appears as `#footer` here". A rule matching nothing never stays silent.
- **Beginner scope.** Only basic properties are required (colour, background,
  fonts, text, margin/padding, border/radius, max-width, list-style). The
  help dialog is a mini CSS toolbox with MDN links
  (https://developer.mozilla.org/).

**Share link** works like HTML Doctor's: `#css=<byte count>.<base64url>` —
the stylesheet travels inside the link, nothing is uploaded, 12,000
characters fit a 16 KB link.

**Classroom embedding:** verified against the campus-apphost contract with the
two-origin harness (reply-first export, per-question draft keys, canonical
share-link base). The export answers with the student's stylesheet, so a
submission contains the CSS, not a picture of the editor.

The preview is a fully sandboxed `<iframe>` fed with the fixed HTML and the
student CSS injected in place of the `<link>` (any `<` in CSS becomes the
`\3c ` escape first, so a stray `</style>` cannot break out). The two photos
(`robot.jpg`, `timetable-app.png`) cannot load in a sandboxed frame, so small
inline SVG drawings stand in for them in the preview — the read-only HTML
keeps the real file names. The page's HTML is trusted and script-free, and
students write CSS only, so nothing they type can execute.

No dependencies, no build step — the whole app is `index.html`. The editor
comes from html-doctor (which was developed as part of
[nyjc-computing/html-inspector](https://github.com/nyjc-computing/html-inspector));
html-doctor's dormant CSS validator (its `LINT_CSS = false` lesson) was
extracted, fixed and switched on here.

**Live:** https://nyjc-computing.github.io/css-stylist/

## The page, the quest and the target look

The fixed page: `h1#top` title, `#intro`/`#cca`/`#projects`/`#contact`/`#footer`
sections (all `class="section"`), a bordered subjects table, two photos, a
contact form, a `span.motto`. The 27 quest items map one-to-one onto the
teacher answer key (`D:\sysadmin\sharing\2026-11-25-css-stylist-answer-key.md`),
which also holds the reference PNG. The reference is embedded as a data URI so
the single file works on GitHub Pages and in the campus embed; to regenerate
it after changing the fixed HTML: render FIXED_HTML + answer stylesheet at a
760 px viewport (with the SVG stand-ins substituted), screenshot the full page,
embed as `REF_IMAGE`.

## Testing

Handy for e2e tests (and the browser console):

- `window.cssStylist` — `setCss(css)` types on your behalf (lint, preview,
  quest and autosave all react), `getCss()`, `getFindings()` (severity, code,
  title, line, column), `getQuest()` (label + done per item), `shareLink(css?)`,
  `previewState()` → `{ stale, fromSharedLink }`.
- `<html data-ready="1">` appears when boot is done; `#pv-note[data-stale]`
  is present from an edit until the preview has re-rendered (400 ms debounce).
- Storage: `cssstylist.css.v1` holds the draft (`cssstylist.css.v1.embed.<qid>`
  when embedded), `cssstylist.split` the divider position, `cssstylist.quest`
  and `cssstylist.refmode` the quest/reference-panel states.
  `?example=starter` loads the starter; a `#css=…` share link overrides both
  until the page is edited.

## Verification (2026-11-25, v2.2.1 — user-behaviour E2E run in a real browser)

- Fresh boot: skeleton + `background-color:` placeholder, exactly one finding
  (css-novalue, colour-shaped fix example), quest 0/28, reference hidden.
- First quest item by keyboard: caret to the placeholder line, real typing of
  ` #eef2f6;` → finding clears, item 1 ticks (1/28), preview settles, draft
  autosaves.
- Typo journey: typing `colour: red;` reports css-unknown-prop ("Did you mean
  'color'?"); the ▲ panel toggle opens the list, clicking the row shows the
  detail with the MDN link; Escape closes; fixing the typo clears the finding.
- Editor keys: Enter after `{` auto-indents 2 spaces; Tab inserts 2 spaces.
- index.html tab: read-only view renders all lines highlighted with line
  numbers; style.css tab restores the editor.
- Quest collapse: label flips to "show ▲", state persists across reload,
  expand restores the list.
- Reference button: hover peeks, leave hides (150 ms), click pins (persisted),
  click unpins.
- Share link: dialog fallback shows a selectable `#css=` link; opening the
  link restores the stylesheet with the shared-link notice; quest recomputes
  (a near-miss colour correctly stays unticked).
- Reload persistence: the draft survives a reload and the quest recomputes.
- Starter (`?example=starter`): 0 findings, exactly its 2 declarations tick.
- Answer journey: the full answer stylesheet yields 0 findings and a
  28/28 *complete* tracker; the preview matches the reference design.
- Reset flow: a wrecked stylesheet (multiple findings, autosaved) → Reset
  opens the confirm dialog → Cancel keeps the draft → Confirm restores the
  skeleton (placeholder + empty rules), leaves exactly the css-novalue
  finding, quest back to 0/28, and the saved draft replaced with the skeleton.
- Invisible characters: a stylesheet whose values carry non-breaking spaces
  (copy-pasted code) — previously silent — now reports css-invisible-char at
  the exact character position; NBSPs inside comments don't false-positive;
  replacing them with normal spaces ticks the quest items.
- `font` shorthand: `font: italic 14px Arial` lints clean; `font: bold`,
  `font: red`, `font: 14px` alone report what's missing. (v2.3.3 regression
  found by a teacher: the v2 port had dropped the shorthand matcher, so
  typing any `font:` declaration threw mid-lint and froze the overlay, quest
  and preview — update() is now guarded so an analysis bug can never freeze
  the editor again.)
- campus-classroom harness on the final bytes: handshake OK, snapshot contains
  the student CSS, app chrome absent, 0 script tags, nonce stamped.
