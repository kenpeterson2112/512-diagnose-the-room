# Build the Lesson

A standalone, single-file drag-and-drop activity for a graduate ed-tech course
presentation on **constructivism**.

The visitor is handed a teaching scenario and one shared pool of ten strategies.
They drag (or tap) exactly **three** strategies into a lesson canvas and get a
qualitative read on how that combination fits *this* context. They do it three
times, across three BC-curriculum settings — an in-person Mathematics 5 lesson, a
fully online Social Studies 10 course, and a blended Science 10 class with some
students remote.

**The thesis:** constructivist *content and endpoints* travel across contexts, but
*facilitation and scaffolding* do not. The activity makes this concrete by reusing
the **same ten strategies** for all three scenarios and **re-tiering** them each
time. A move that's a strong fit in person can be inert online, and vice versa.

---

## What's in this repo

| File | Purpose |
|------|---------|
| `index.html` | The entire activity — inline CSS + JS, zero external dependencies. |
| `README.md` | This file. |

`index.html` uses **no** external files, **no** build step, **no** network
requests, and **no** `localStorage`/`sessionStorage`, so it runs inside a sandboxed
iframe. It is responsive to ~360px, works fully on touch **without drag** (tap to
place, tap to remove), is keyboard operable with visible focus, respects
`prefers-reduced-motion`, and adapts to light/dark system themes.

The layout is deliberately **compact for a tight embed**: the canvas is always a
single 3-across row (it never stacks into three tall rows), the strategy pool is a
wrapping cloud of short chips rather than a full-width list, and padding and copy
are trimmed. At 360px wide the tallest screen (the builder) is roughly 700px tall.

### How the interaction works

- **Tap / click** a pool chip to drop it into the next open slot; **tap / click** a
  chip already in the canvas to send it back to the pool.
- **Drag and drop** works too (chip → canvas to place, chip → pool to remove) as a
  desktop enhancement, but nothing depends on it.
- **Keyboard:** chips are native buttons — Tab to move, Enter/Space to place or
  remove. The tap logic and the keyboard logic are the same code path.
- Submit stays disabled until exactly three strategies are placed.

## Trying it locally

Open `index.html` in any browser — double-click it or drag it onto a browser
window. No server required.

---

## Embedding in Google Sites

Google Sites embeds a page by URL, so `index.html` needs to live at a public URL
first.

### Option A — Host the file, embed by URL (recommended)

1. Put `index.html` somewhere that serves it publicly. Free options:
   - **GitHub Pages** — push this repo, enable Pages (Settings → Pages → deploy from
     branch); your file lands at
     `https://<your-username>.github.io/<repo>/index.html`.
   - **Netlify Drop** (<https://app.netlify.com/drop>) — drag the file in, get a URL.
2. In Google Sites, open your page and click **Insert → Embed**.
3. Choose the **By URL** tab, paste the public URL, and click **Insert**.
4. Drag the block to resize it. A width that fills the column and a height around
   **720–780px** comfortably fits the tallest screen; on wider desktops it needs
   less. Let the iframe scroll if a viewer's device runs shorter.

### Option B — Paste the code directly

1. In Google Sites, click **Insert → Embed**.
2. Choose the **Embed code** tab.
3. Open `index.html` in a text editor, copy the **entire** contents, paste it in,
   and click **Insert**.

> **Note:** Google's embed-code box has a size limit and sometimes strips
> `<script>`. If Option B renders blank, use **Option A** — a hosted URL in an
> `<iframe>` is the reliable path this file is built for.

### Sizing tips

- The activity manages its own scrolling inside the iframe; give the embed a fixed
  height and let content scroll within it.
- If it feels cramped on phones, increase the embed **height**, not the width — the
  layout is already capped at a readable width and centers itself.

---

## Editing the content (no coding required)

**All editable content lives in two data structures** near the top of the
`<script>` block in `index.html`, under the big comment banner. Everything below the
line that reads *"ENGINE — content editors normally don't need to change below"* is
the machinery and shouldn't need touching to change wording, tiers, or feedback.

### 1. `STRATEGIES` — the one shared pool of ten chips

```js
{ id: 3, func: "collab", label: "Small-group task" }
```

- `id` — a stable number. **Keep it unchanged** once set; the scenarios refer to
  strategies by this id.
- `label` — the text on the chip. **Keep labels short (2–4 words)** — they have to
  fit inside a 3-across canvas slot at 360px wide, so long labels will make the
  canvas grow tall again. Chips are uniform in styling on purpose so nothing
  telegraphs quality.
- `func` — the *job* the strategy does. This drives the whole-combination feedback
  (e.g. "nothing here reads where learners are"). Use one of:
  `anchor`, `collab`, `ownership`, `presence`, `diagnosis`, `compliance`,
  `transmission`. Plain-language names for these live in the `FUNC_LABEL` object
  right below `STRATEGIES` — edit those if you rename or add a function.

There is **one** pool, shared by every scenario. Do not split it into three.

### 2. `SCENARIOS` — the three contexts, each re-tiering all ten

```js
{
  id: "inperson",
  mode: "In person · one class period",   // the little grey tag
  short: "in person",                       // used on the closing screen
  title: "Mathematics 5",
  setup: "2–3 sentence setup: subject, learners, constraint.",
  tiers: {
    // EVERY strategy id must appear here, each with a tier + one-sentence note.
    8:  { tier: "strong",   note: "Circulating and listening is real-time diagnosis…" },
    4:  { tier: "weak",     note: "A co-edited digital doc fights the setting when…" },
    // …all ten…
  }
}
```

Each scenario's `tiers` object maps **every** strategy id to:

- `tier` — one of exactly four strings:
  - `"strong"` — a strong fit here
  - `"workable"` — a real, defensible **tradeoff** (describe it as a tradeoff, not a
    mistake)
  - `"weak"` — weak or actively counterproductive here
  - `"unavailable"` — the strategy has **no medium** in this context. The chip still
    shows in the pool but is greyed out and can't be placed. (Used for *"circulate
    and listen"* in the fully asynchronous course, where there's no live talk.)
- `note` — one short sentence on why it does or doesn't fit **this** scenario.

**Design target per scenario:** 3 `strong`, 3 `workable`, 4 `weak` — or, for the
online scenario, 3 / 3 / 3 plus one `unavailable`. The activity will still run with a
different mix, but that balance keeps every scenario's ranking defensible and the
comparison meaningful.

### Rules of thumb

- **The point is re-tiering, not a right answer.** Make sure some strategies swing
  hard between scenarios (strong in one, weak in another) — that swing is what the
  visitor is meant to notice.
- **Two strategies are deliberately weak everywhere for different reasons:** the
  recorded-lecture-and-quiz (`transmission`) and the required-graded-posts
  (`compliance` that resembles constructivism without producing it). Keep that
  contrast if you edit them.
- **Feedback may name the ideas** of Powell & Kalina (2009), Singh, Steele & Singh
  (2021), Schrader (2015), Gulati (2008), and von Glasersfeld (2008) in plain prose.
  Do **not** invent quotes or page numbers — name the idea, not a fake citation.
  Formal citations are added to the presentation separately.

### The combination feedback and closing screen

- The one-sentence **combination** verdict after each submit is **computed** from the
  chosen strategies' `tier` and `func` values (see `comboFeedback()` in the engine).
  You normally shape it by editing tiers and functions, not by writing the sentence
  directly.
- The **closing screen** compares strategies the visitor reused across scenarios and
  whether their value held. It's automatic; the reflection question the visitor can
  copy is the `question` string inside `renderClosing()`.

Adding, removing, or reordering strategies and scenarios is fine — the progress
indicator ("Scenario 2 of 3"), the canvas slot count, and the closing analysis all
adjust themselves. Just make sure every scenario's `tiers` lists every strategy id.

---

## Accessibility & compatibility notes

- Chips and navigation are native `<button>` elements: Enter/Space activate, Tab
  moves, focus is visible, and focus follows a chip as it moves between pool and
  canvas.
- Touch works completely without drag (tap to place, tap to remove). Drag-and-drop
  is a desktop enhancement layered on the same actions.
- Motion is limited to gentle fades, all disabled under `prefers-reduced-motion`.
- The "Copy the question" button uses the Clipboard API with a text-selection
  fallback for iframes/older browsers.
- No cookies, no storage, no analytics, no external calls.
