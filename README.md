# Diagnose the Room

A standalone, single-file branching classroom-scenario simulator for a graduate
ed-tech course presentation on **constructivism**.

The visitor plays a teacher facing five short classroom moments — in person and
online — and chooses what a constructivist teacher does next. The activity's
thesis is that constructivist teaching is a **real-time diagnostic loop** (reading
where a learner's schema sits, calibrating scaffolding to a live zone of proximal
development), not a fixed activity type you schedule.

Every choice is tagged as one of three response styles:

- **Diagnose & scaffold** — genuine constructivist diagnosis and support.
- **Tell & deliver** — transmission / direct telling.
- **Structure & enforce** — compliance-enforcement that *looks* constructivist but
  is really about control. This is the interesting trap: it operationalizes
  Gulati's critique that mandatory participation can fake constructivism.

There are no grades and no failure state. At the end the visitor sees a **profile
of their tendencies** (not a score) plus one open reflection question to seed the
course discussion forum.

---

## What's in this repo

| File | Purpose |
|------|---------|
| `index.html` | The entire activity — inline CSS + JS, zero external dependencies. |
| `README.md` | This file. |

`index.html` uses **no** external files, **no** build step, **no** network
requests, and **no** `localStorage`/`sessionStorage`, so it runs happily inside a
sandboxed iframe. It's responsive down to ~360px, supports keyboard navigation
with visible focus, respects `prefers-reduced-motion`, and adapts to light/dark
system themes.

## Trying it locally

Just open `index.html` in any browser — double-click it, or drag it onto a browser
window. No server required.

---

## Embedding in Google Sites

Google Sites can embed a page by URL, so `index.html` needs to live at a public
URL first. Two easy routes:

### Option A — Host the file, embed by URL (recommended)

1. Put `index.html` somewhere that serves it publicly. Free options:
   - **GitHub Pages** — push this repo, enable Pages (Settings → Pages → deploy
     from branch), and your file will be at
     `https://<your-username>.github.io/<repo>/index.html`.
   - **Netlify Drop** (<https://app.netlify.com/drop>) — drag the file in, get a URL.
2. In Google Sites, open your page and click **Insert → Embed**.
3. Choose the **By URL** tab, paste the public URL, and click **Insert**.
4. Drag the resulting block to resize it. Give it plenty of height — the activity
   is tall on narrow screens. A width that fills the column and a height of roughly
   **720–820px** works well.

### Option B — Paste the code directly

1. In Google Sites, click **Insert → Embed**.
2. Choose the **Embed code** tab.
3. Open `index.html` in a text editor, copy the **entire** contents, and paste it
   into the box. Click **Insert**.

> **Note:** Google's embed-code box has a size limit and sometimes strips
> `<script>`. If Option B renders blank or shows only text, use **Option A** — a
> hosted URL in an `<iframe>` is the reliable path and is what this file is built
> for.

### Sizing tips

- The activity assumes it controls its own scrolling inside the iframe. Give the
  embed a fixed height and let content scroll within it.
- If it feels cramped on phones, increase the embed height rather than the width —
  the layout is already capped at a readable line length and centers itself.

---

## Editing the content (no coding required)

**All scenario text lives in one place:** the `SCENARIOS` array near the top of
the `<script>` block in `index.html`, under the big comment banner that says
*"CONTENT LIVES IN THE `SCENARIOS` ARRAY BELOW."* You can rewrite wording, add,
remove, or reorder scenarios without touching any logic.

### The shape of one scenario

```js
{
  id: "seasons",                     // any short unique string
  setting: "In person · 7th-grade science",  // the little grey tag
  heading: "The confident wrong answer",     // the scenario's title
  setup:
    "A 2–4 sentence vivid setup. Set the scene; make it feel real.",
  prompt: "What do you do next?",    // the question above the choices
  choices: [
    {
      text: "The choice the visitor clicks. Make it genuinely tempting.",
      style: "transmission",         // see the three styles below
      consequence:
        "What plausibly happens next in the room. Show, don't grade.",
      debrief:
        "One short paragraph naming the underlying principle in plain language."
    },
    // ...two more choices...
  ]
}
```

### Rules of thumb when editing

- **`style` must be one of exactly three strings** (spelled exactly like this):
  - `"constructivist"` — genuine diagnosis / scaffolding to a live ZPD
  - `"transmission"` — direct telling / delivering the right answer
  - `"compliance"` — enforcing participation; looks active, is really control

  These tags are what build the end-screen profile. If you misspell one, that
  choice won't be counted, so copy-paste them.

- **Give each scenario three choices** — one of each style is the intended design
  (a real constructivist move, a tempting transmission move, and a plausible
  compliance move). The engine will accept two or more choices per scenario if you
  want to experiment, and will letter them A, B, C… automatically.

- **Keep the compliance choice genuinely tempting.** The whole point is that it's
  something well-meaning teachers really do — not an obvious wrong answer.

- **Debriefs may name the ideas** of Powell & Kalina (2009), Singh, Steele & Singh
  (2021), Schrader (2015), Gulati (2008), and von Glasersfeld (2008) in plain
  prose. Do **not** invent quotes or page numbers — name the idea, not a fake
  citation. Formal citations get added to the presentation separately.

- **Number of scenarios is automatic.** Add or remove entries and the progress
  indicator ("Moment 2 of 5"), the profile totals, and the "next / see profile"
  button all adjust themselves. No other edits needed.

### Editing the end screen

- The **profile labels and one-line blurbs** live in the `STYLE_META` object right
  after the `SCENARIOS` array.
- The **narrative read-out** (the paragraph interpreting the visitor's mix) lives
  in the `profileReadout()` function just below that.
- The **reflection question** the visitor copies is the `question` string inside
  `renderEnd()` (search for `"Take this to the forum"`).

Everything below the comment line that reads *"LOGIC BELOW — content editors
normally don't need to change this"* is the engine. You shouldn't need to touch it
to change wording or scenarios.

---

## Accessibility & compatibility notes

- Choices and navigation are native `<button>` elements, so **Enter** and **Space**
  activate them and Tab moves between them, with a visible focus ring.
- Each new scene moves keyboard focus to its heading and announces via an
  `aria-live` region.
- Motion is limited to gentle fades and bar fills, all disabled under
  `prefers-reduced-motion`.
- The "Copy the question" button uses the Clipboard API with a text-selection
  fallback for iframes/older browsers; if both fail, the question is right there to
  select by hand.
- No cookies, no storage, no analytics, no external calls.
