# The Ark of the Covenant — A Scriptural Archive

A single-page reference site devoted to the ark of the covenant as it appears in the Holy Scriptures:
**articles**, a **searchable scripture index**, a **chronicle of the ark**, and **trivia**.

Nothing is loaded from a CDN and there is no build step, no framework and no dependencies — just HTML,
CSS and plain ES modules, so it runs anywhere a static file is served.

- **Live (Perchance):** https://perchance.org/trivia-arkofthecovenantofiam

---

## Repository layout

| Path | Role |
| --- | --- |
| `index.html` | The whole page: markup and all styling (one `<style>` block, CSS custom properties in `:root`). Loads `src/app.js` as a module. Section counts are filled at runtime from the content lists. |
| `src/content.js` | **All content.** `SITE`, `HERO_FACTS`, `ARTICLES` (16), `SCRIPTURES` (68 passages), `TIMELINE` (19 entries), `TRIVIA` (26 questions), `RANKS`, `HONORIFICS`. |
| `src/app.js` | All rendering and interaction: the hero ark illustration (inline SVG), article cards and the reader overlay, scripture search, the chronicle, and the name gate → trivia flow. |
| `perchance/main.pjs` | The Perchance listing's list/config file: `$meta` (title, description, tags, social thumbnail) and `arkSiteConfig` (hero subtitle, dedication, default form of address). |
| `perchance/index.html` | The Perchance version's body fragment — the same markup as `index.html`, without the `<!DOCTYPE …>`/`<head>` wrapper. |

Content is deliberately kept apart from markup: adding an article, a verse or a question means adding one
object to a list in `src/content.js`, and changing nothing else.

---

## Deploying on GitHub Pages

1. Push these files to a repository (the folder tree exactly as it appears here).
2. In the repository: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, then
   branch `main` and folder `/ (root)`. Save.
3. After a minute the site is live at `https://<your-username>.github.io/<repository-name>/`.

`index.html` and `src/app.js` reference each other by relative path and the app has no server-side
requirements, so it works unmodified. (Open it through a web server — Pages, `python3 -m http.server`,
etc. — rather than by double-clicking the file, because browsers block ES modules loaded from `file://`.)

---

## Editing the content

Everything a reader sees lives in `src/content.js`.

**Add or change an article** — append an object to `ARTICLES`:

```js
{
  id: "short-slug",                 // used for #anchors
  no: "XVII",                       // shown number — Roman numerals, numbered by hand
  title: "Article title",
  subtitle: "One line under the title",
  refs: ["Exodus 25:10", "Hebrews 9:4"],
  body: [ /* blocks, below */ ]
}
```

Article bodies are arrays of blocks rendered by `blockHtml()` in `src/app.js`:

| Block | Shape | Notes |
| --- | --- | --- |
| Paragraph | `{ t:"p", x:"…" }` | inline `<em>…</em>` allowed |
| Sub-heading | `{ t:"h", x:"…" }` | escaped — keep it plain text |
| Pull quote | `{ t:"q", x:"…", ref:"Exodus 25:22" }` | |
| Note box | `{ t:"note", x:"…" }` | |
| Bulleted list | `{ t:"list", items:["…","…"] }` | inline `<em>` allowed |
| Tier cards | `{ t:"tiers", items:[{ tier, badge, title, x }] }` | the "what is written / handed down / claimed" cards |

Paragraphs, pull quotes, notes and list items are inserted as raw HTML (so `<em>` works); headings and tier
titles are escaped.

**Add a passage to the index** — append to `SCRIPTURES`:

```js
{ ref: "1 Kings 8:9", text: "…", tags: "solomon temple cloud" }
```

The `tags` string is what the search box matches on, so add the words a reader would actually type.

**Add a question** — append to `TRIVIA` (each has a prompt, answer options with a `correct` flag, and an
explanatory note shown after answering).

The article register — the `no` field is manual, so keep it in order when inserting:

> I Pattern Shown in the Mount · II Acacia Wood and Pure Gold · III What Lay Within: The Testimonies ·
> IV Between the Cherubim · V The Name That Dwells Between the Cherubim · VI The Veil, the Priest, and the
> Blood · VII The Golden Censer and the Veil Rent · VIII Rise Up, LORD: The Ark on the March ·
> IX Jordan Divided, Jericho Fallen · X Captivity, Dagon, and the Return · XI Uzzah, Obed-edom, and David's
> Dance · XII Singers, Trumpets, and the Songs of the Ark · XIII The Temple, the Cloud, and Jeremiah's Word ·
> XIV The Second Temple, and the Throne That Was Not There · XV Hilasterion: The Mercy Seat in the New
> Testament · XVI Traditions, Claims, and the Long Silence.

### Updating the Perchance version

`perchance/index.html` is the same markup as `index.html` but without the document wrapper, and
`perchance/main.pjs` is pasted into the Perchance editor's list panel. If you edit `index.html` or the
files in `src/`, mirror the change into `perchance/` (or the other way round) so the two stay in step.

---

## Two deliberate design rules

### 1. The name gate

The Trivia section stays sealed until the reader gives a name. It is asked by an inline panel — not a
popup — and the answer is kept in the reader's own browser under `ark.viewer.name` / `ark.viewer.honorific`,
then used to address them ("Well met, Seeker Anna") on each question and on the result card. A **Change
name** button clears it.

**Nothing is transmitted anywhere.** There is no server, no analytics and no account; the name never
leaves the device. If you ever add analytics, keep it that way.

### 2. Scroll behaviour in the quiz — please don't regress it

Answering a question must not move the page. Focusing a button normally scrolls the browser down to it,
which used to drag the view away from the question the reader was looking at; the fix is
`focus({ preventScroll: true })` plus two helpers in `src/app.js`:

- `alignToStart(el)` — on each new question and on the result, brings the panel top back to the
  `scroll-padding-top` line just under the sticky nav, so the question always appears in the same place.
- `revealQuizControls()` — after answering, if the Next button is below the fold it scrolls the *minimum*
  amount needed, and never enough to push the question off the top. On a desktop-sized viewport it does
  nothing at all.

---

## Epistemic discipline (important — please don't blur it)

The site distinguishes four tiers of material and labels them wherever they appear: canonical Scripture;
deuterocanonical (2 Maccabees); the ancient tradition of a living church (Ethiopian / Aksum, *Kebra
Nagast*); and the modern unverified claim (the reported 1980s find near Jerusalem). The footer states the
rule plainly. New content should stay inside that discipline — no claim presented above its tier, and the
scripture index kept canonical-only.

---

## Notes

- **Scripture text** is the Authorised (King James) Version, which is in the public domain.
- **Palette:** near-black background with deep purple and cobalt accents and grey secondary text, defined
  as CSS custom properties at the top of `index.html`.
- **The ark illustration** in the hero is an original inline SVG written for this site — no image files,
  and nothing to attribute.
- **Licence:** none is included. Add one if you intend others to reuse the code.
