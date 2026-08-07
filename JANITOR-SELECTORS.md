# JanitorAI selector map

What can actually be targeted on a JanitorAI profile page, and how.

Observed from working community templates (Hime, `@yourhighness08`) rather than
official docs. None of this is documented by JanitorAI, so it can break on any
redeploy. Class *names* are facts about the platform, not anyone's design — the
techniques below are noted so the crypt can use them without copying anyone's
stylesheet.

Most of the map below is read off Hime's **Dark Red** template, which she
released free for public use and modification. Credit where it is due: without
it, half of these selectors would still be guesswork. Her rule is the sensible
one — take the ideas, don't take a layout wholesale and call it yours.

## Two kinds of class name

**Semantic, stable — use these.** They read like names a developer chose, and
they survive redeploys:

| Selector | What it is |
|---|---|
| `.pp-uc-about-me` | the container your About Me paste lands in |
| `.pp-uc-avatar` | profile picture |
| `.pp-uc-title` | the username heading |
| `.pp-uc-background` | the bio card's background panel |
| `.profile-info-stack` | outer column holding the whole profile block |
| `.profile-info-hstack` | row holding avatar + name |
| `.profile-page-flex` | wrapper around bio *and* the bot card list |
| `.profile-background-effect-image` | the blurred backdrop image |

**Hashed, unstable — avoid.** Emotion/CSS-in-JS output like `.css-15w88gn`,
`.css-1q7rmf0`. The hash is regenerated whenever the site is rebuilt, so a rule
written against one works until it silently doesn't.

When there is no semantic class, match on a substring instead:

```css
[class*="contentWrapper"]   /* survives .css-1abc-contentWrapper-xyz */
[class*="wrapper"]
```

Only the hash changes; the readable part usually stays.

**Not real:** `.character-card__name`, `.character-card__wrapper` and the rest
of the `.character-card__*` family. JanitorAI does not ship those. Our
`profile-css.txt` is built almost entirely on them, which is why it appears to
do nothing — see PASTE-HERE.md. The real bot-card names are all `pp-cc-*`,
listed below.

## The bot cards

The part `profile-css.txt` was guessing at. These are real.

| Selector | What it is |
|---|---|
| `.profile-character-card-stack` | the card itself — make this the grid |
| `.pp-cc-wrapper` | outer wrapper, the hook for per-card variants |
| `.pp-cc-name` | character name |
| `.pp-cc-avatar` | the portrait |
| `.profile-character-card-avatar-aspect-ratio` | portrait's aspect box |
| `.pp-cc-description` | the blurb |
| `.profile-character-card-description-box` | blurb's container |
| `.pp-cc-tags`, `.pp-cc-tags-wrap` | tag row and its pills |
| `.pp-cc-tags-regular`, `.pp-tag-<tagname>` | one class per tag, lowercased |
| `.pp-cc-chats-count`, `.pp-cc-public-chats-count` | the counts |
| `.profile-character-card-stats-box` | stats cluster |
| `.pp-cc-ribbon`, `.pp-cc-ribbon-wrap` | the corner ribbon |
| `.pp-cc-creator-name`, `.pp-cc-star-line` | usually hidden on a themed profile |
| `.pp-cc-star` | the star itself, separate from its line |
| `.pp-cc-list-container` | the container holding every card |
| `.pp-cc-tags-wrap-regular`, `.pp-cc-tags-custom` | built-in vs `#hashtag` tags |

Hashed ones current as of Puppy's collection, and expected to rot:
`.css-1s5evre` the card · `.css-1q7rmf0` the avatar box · `.css-k1urot` the
tag row · `.css-jdhqy4` tooltips. Prefer the `pp-cc-*` names wherever one
exists.

**Cards side by side instead of stacked** — worth knowing for the vault:

```css
.pp-cc-list-container { display: flex; flex-direction: row; overflow-x: auto; overflow-y: hidden; }
.pp-cc-wrapper { min-width: 12rem; }
```

**Fade the bottom of a card's portrait** — `mask-image` works, only
`mask-composite` is disabled:

```css
.pp-cc-avatar { mask-image: linear-gradient(to bottom, #000, #000 80%, #0000 98%); }
```

**The article-card layout** is a grid on the card with named areas — portrait
left, name across the top, blurb and tags right, a series label along the foot:

```css
.profile-character-card-stack {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-areas:
    "name  name"
    "pfp   desc"
    "pfp   tags"
    "serie serie";
}
.pp-cc-name { grid-area: name; }
.profile-character-card-avatar-aspect-ratio { grid-area: pfp; }
.profile-character-card-description-box { grid-area: desc; }
.pp-cc-tags { grid-area: tags; }
```

**Label a card by its tag.** Every tag on a bot becomes a class, so a card can
be labelled with the series it belongs to — this is how the crypt could stamp
each vessel with its world:

```css
.profile-character-card-stack::after { content: 'standalone'; grid-area: serie; }
.pp-cc-wrapper:has(.pp-tag-losttales) .profile-character-card-stack::after {
  content: 'THE LOST TALES';
}
```

**Rename a tag** the same way — zero the text, draw your own:

```css
.pp-cc-tags-regular.pp-tag-deaddove { font-size: 0; }
.pp-cc-tags-regular.pp-tag-deaddove::before { content: "Dead Dove"; font-size: .75rem; }
```

## The page furniture

| Selector | What it is |
|---|---|
| `.pp-page-background` | the page's backdrop layer |
| `.pp-top-bar`, `.pp-top-bar-left`, `.pp-top-bar-right` | the site header |
| `.glow-logo h2`, `.glow-logo p` | **the JanitorAI wordmark** — zero and redraw |
| `.pp-top-bar-search-input`, `#search-input` | site search |
| `.pp-top-bar-app-menu-list-item` | the account dropdown |
| `.pp-mnb-wrapper` | mobile nav bar |
| `.pp-uc-title` | your username |
| `.pp-uc-followers-count` | follower count |
| `.pp-uc-avatar-container`, `.pp-uc-member-since` | avatar, join date |
| `.profile-badges`, `.profile-badge` | the site-awarded badges |
| `#profile-tabs::before` | **the heading above your bot list** — "296 laments" lives here |
| `.pp-tabs-button`, `.pp-tabs-indicator` | the tab strip, usually hidden |
| `.pp-pg-total p`, `.Btn2-purple strong` | the character count |
| `.pp-fl-search-input`, `.pp-fl-filter-button` | the profile's own search and filter |
| `.pp-pg-page-button` | pagination numbers |

## What JanitorAI disables

There is an official list, maintained by Puppy and MantaRae in the JanitorAI
CSS Collection carrd. It supersedes anything worked out by trial and error
here, including two things this file previously got wrong.

**Tags:** `<script>` · `<button>` · `<input type>` · `<label>` · `<svg>` ·
`<path>` · `<textpath>` · `<video src>` · `<audio>`

**Properties:** `url()` · `scrollbar-width` · `scrollbar-color` ·
`scrollbar-gutter` · `overflow-clip-margin` · `scroll-behaviour` ·
`scroll-margin` · `scroll-padding` · every `scroll-snap-*` ·
`column-gap` · `row-gap` · `place-items` · `mask-composite` ·
every `offset-*` · `-inline-start/end` · `-block-start/end`

**At-rules and features:** `@container` · `@property` · CSS nesting ·
**CSS variables** · `attr(data-text)` · **ARIA attributes** ·
**the `name` attribute on `<details>`**

### Two corrections to what this file used to say

**`<details name="...">` does not work.** The `name` attribute is disabled, so
the exclusive-accordion trick — open one, the others close — is not available.

**But a tab strip is still possible**, which this file previously said it was
not. Io's method, from Puppy's collection: give each tab `tabindex="0"` so it
can take focus, then drive the panels off `:focus` and sibling combinators.
No `<details>`, no script.

```css
.tab:nth-of-type(1):focus ~ .wrap .panel:nth-of-type(1),
.tab:nth-of-type(2):focus ~ .wrap .panel:nth-of-type(2) { opacity: 1; }
```

```html
<div tabindex="0" class="tab">about me</div>
<div tabindex="0" class="tab">rules</div>
<div class="wrap"><div class="panel">…</div><div class="panel">…</div></div>
```

The catch is that focus is lost on a click elsewhere, so a panel closes when
you click away. That is a fair trade for thirteen seals collapsing to a strip.

**CSS variables are disabled.** Every colour has to be written out in full.
No `--oxblood: #700e1a` and `var(--oxblood)` afterwards.

### Workarounds the carrd suggests

| Disabled | Instead |
|---|---|
| `<button>`, `<input>` | a `<div>` or `<span>` styled to look like one |
| `<svg>`, `<path>` | Unicode — ★ ✔ ⬤ ▲ ❦ — or `border-radius` / `clip-path` |
| `<label>` | a styled `<span>` beside the fake control |
| scroll / overflow properties | fixed-height container plus `overflow: auto` |
| `mask-composite` | `clip-path`, or gradient overlays |

## Where code is allowed at all

**Profile only.** In the carrd's words: *"Customization is profile-only. Other
site areas allow limited HTML and no CSS."*

| Surface | HTML | `<style>` | Inline `style=""` |
|---|---|---|---|
| Settings → About Me | ✅ | ✅ | ✅ |
| Profile CSS Editor | — | CSS only, no tags | — |
| **Character description** | limited | ❌ **stripped** | ✅ |
| Anywhere else | limited | ❌ | ✅ |

A `<style>` block pasted into a character description renders as visible text —
the tags print on the page. Character pages must be inline-styled throughout,
which rules out `:hover`, `@keyframes`, `@media` and pseudo-elements there.

Two more from the carrd worth remembering: **there is no autosave and the CSS
Editor has no undo**, so keep the repo copy authoritative; and **saving in one
editor can overwrite the other**, so stick to About Me and leave the in-page
editor alone.

Source: the JanitorAI CSS Collection carrd, maintained by MantaRae and Puppy.

## The trick worth knowing

```css
.pp-uc-about-me { display: contents; }
```

`display: contents` removes the About Me box from the layout while keeping its
children. Those children then become direct items of the profile grid above
them, so a paste can lay out the *whole page* — cover image, sidebar, dropdown
column, creators panel — instead of being trapped inside one bio card.

That is the mechanism behind every paid theme with a sidebar or a two-column
bio. Combine it with a grid on the parent:

```css
@media screen and (min-width: 768px) {
  .profile-info-stack:first-child {
    display: grid;
    grid-template-columns: minmax(300px, 900px) minmax(300px, 900px);
    grid-template-areas:
      "cover  cover"
      "left   right"
      ".      follow";
  }
}
```

Each child then claims its cell with `grid-area: cover`, and so on.

## Other techniques observed

**Replace the username with display type** — zero out the real text, draw your
own in a pseudo-element:

```css
.pp-uc-title { font-size: 0; }
.pp-uc-title::after { content: 'BELOVED ANGEL'; font-size: clamp(4rem, 6vw, 6rem); }
```

**Horizontal scroll row** — for THE WORLDS and the devotion groups:

```css
.row   { display: flex; gap: 20px; overflow-x: auto; }
.card  { width: 300px; flex-shrink: 0; scroll-snap-align: start; }
```

**Stack bio above the bot cards** — `.profile-page-flex { flex-direction: column; }`

**Unrestrict the bio card's width** — `.pp-uc-background { max-width: unset; }`

## Observed on live profiles

Things seen working on real pages (Hime's *Carnal Archive*, Neri's *Come in,
Angel*, *Noli Timere Messorem*, *Circus Troupe*) that go past the bio box:

**The site's own wordmark can be replaced.** Top-left reads "Carnal Archive" or
"Come in, Angel" instead of JanitorAI. Same `font-size: 0` + `::after { content }`
move as the username.

**So can the counts and placeholders.** "296 laments", "15 reaped souls",
"2360 together, under the snowfield" in place of the follower count; "search
lament…", "Do not fear the reaper" in place of the search placeholder. Anything
rendered as text can be zeroed and redrawn.

**Sticky bottom bar** — *Noli Timere* pins a strip along the foot of the page
carrying a label and the search field. `position: fixed; bottom: 0`.

**Frosted glass** — *Come in, Angel* floats near-transparent panels over a
full-bleed background with `backdrop-filter: blur()`, which suits a misty
palette far better than solid fills.

**Full-bleed background behind everything**, header included — not just behind
the bio. Our `profile-css-lean.txt` already does this on `body`.

**A portrait row overlapping the hero** — five character plates centred beneath
the display name, breaking the boundary between cover and content.

**A page doll** — the local name for a cut-out pinned to the corner that stays
put while the page scrolls. `position: fixed`, a high `z-index`, and a slow
float. The crypt's raven is one.

**Relabel the follower count** precisely, rather than hiding the lot:

```css
.pp-uc-followers-count span:nth-of-type(2) { font-size: 0; }
.pp-uc-followers-count::after { content: "angels"; }
```

**Attach animation rather than switching it off.** Tigerdropped's pattern, and
the safer way round — nothing to forget to disable:

```css
@media screen and (prefers-reduced-motion: no-preference) {
  .thing { animation: float 3s ease-in-out infinite; }
}
```

**Left sidebar** — *Circus Troupe* puts avatar, follow button, stats and a
stacked nav column down the left, cards to the right. Grid areas on
`.profile-info-stack`, same mechanism as above.

Every one of these is reachable from a single About Me paste.

## What survives the sanitizer

| | |
|---|---|
| `<style>` blocks in About Me | ✅ works — the bought themes ship four in one paste |
| `<details>` / `<summary>` | ✅ works — the crypt's seals rely on it |
| `::before` / `::after` with `content:` | ✅ works |
| `@media` queries | ✅ works |
| Inline `style=""` | ✅ always works |
| `<iframe>` | ❌ stripped — killed the SoundCloud player |
| `<script>` | ❌ stripped |

Because `<style>` works in About Me, the CSS Editor is optional. One paste into
About Me can carry both the styling and the content.

## Image hosting

GitHub Pages works (`belovedangel02.github.io/darts-of-doom/...`) and is what
the crypt uses. Community templates lean on `imgur.com` and `file.garden`.
Any of them is fine as long as the URL points straight at the image file.
