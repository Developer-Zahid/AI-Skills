---
name: tinyflow-style-guide
description: "Build pages, sections, and components on top of the Tinyflow Common Style Guide — its `u-*` utility classes, `cc-*` combo classes, `.container`, fluid-typography variables (Fluid Builder), the `.page-wrapper`/Global-Styles page structure, the site-wide Footer Custom Code (jQuery) behaviors for navbar height, skip link, mobile-nav close, current year, and the FAQ accordion, the shared `.button` and `<details>`-based FAQ Card, the reference JS toolkit (fluid-size/hover helpers, lazy-loaded Swiper/Splide/Lenis, social-share, AI-summarize, and blog-CTA-injection patterns), with exact code in `references/js-reference.md`. Use this skill whenever the user asks for Webflow markup, a new section or page, a hero/CTA/FAQ/footer/navbar/button/slider/marquee/blog-post build, or mentions the Tinyflow style guide, `u-heading-*`, `u-text-md`, `u-spacing-block`, `cc-top-0`, `u-line-clamp`, `u-sr-only`, `page-wrapper`, `data-faq`, `data-target`, `data-swiper-slider`, `data-splide-slider`, `data-blog-component-position`, or Global Styles — even if they never say 'style guide' out loud. Also use when auditing existing markup for style-guide compliance, when converting a design into classes, or when asked which class, variable, data attribute, or JS snippet to reach for to achieve a given style or behavior."
---

# Tinyflow Common Style Guide

Live reference: https://tinyflow-common-style-guides.webflow.io/styleguide
Exact JS code (Footer Custom Code script, reusable toolkit, blog CTA injection): [`references/js-reference.md`](https://raw.githubusercontent.com/Developer-Zahid/AI-Skills/refs/heads/main/tinyflow-common-style-guide-for-webflow/js-reference.md)

Everything produced here is pasted into a Webflow project that **already has this style guide installed**. Every `u-*` and `cc-*` class in this document already exists with styles attached. Treat them as read-only primitives — the whole point of the system is that a hundred pages share one definition of "h2" or "section padding", so redefining one locally silently forks the design system.

## The three class layers

Webflow applies classes in the order they sit on the element, so order carries meaning.

| Layer | Prefix | Owns | Written by you? |
|---|---|---|---|
| Component | none (`hero_title`) | Layout, sizing, and anything unique to this build | Yes — this is where all new CSS goes |
| Utility | `u-` | Shared, repeated design decisions | No — already defined |
| Combo | `cc-` | A modifier on the class immediately before it | No — already defined |

Order on the element: **component first, then utilities, then combos.**

```html
<div class="hero_spacing u-spacing-block cc-bottom-0">
```

The component class goes first so the element is identifiable in the Navigator and targetable in CSS. Utilities go next because they carry the shared styling. Combos go last because they modify what precedes them — `cc-bottom-0` is meaningless without `u-spacing-block` on the same element.

Combo classes are always scoped in CSS, never on their own:

```css
/* correct */   .u-spacing-block.cc-top-0 { }
/* wrong */     .cc-top-0 { }
```

## How classes attach to elements in the Designer

This distinction only matters when building directly in the Webflow Designer (not when just writing HTML) — but it matters a lot there:

- **Component classes and combo classes** connect through Webflow's native Style/selector panel (the normal "+" add-class flow). These are meant to be visible and editable as selectors.
- **Utility (`u-*`) classes never go in that selector panel.** Attach them instead as a custom `class` attribute (Settings → Add Attribute → name `class`, value the utility class name(s), space-separated for more than one). This keeps the native selector chain limited to the classes someone might actually want to edit, and keeps utilities from being accidentally modified through an element that happens to have one applied.

When producing raw HTML for pasting, this is invisible — `class="hero_title u-heading-h1"` looks the same either way. It matters when using Designer tooling (WHTML builder, element/style tools) that distinguishes "native class" from "attribute class": route `u-*` names through the attribute-class path, and only the component/combo classes through the native class-selector path.

## Class inventory

### Headings

`u-heading-display`, `u-heading-h1`, `u-heading-h2`, `u-heading-h3`, `u-heading-h4`, `u-heading-h5`, `u-heading-h6`

The HTML tag is the semantic level; the utility is the visual size. Decouple them freely — a card title that is visually small but structurally an `<h2>` is `<h2 class="card_title u-heading-h5">`. Keep exactly one `<h1>` per page regardless of which heading utility it wears.

### Body text

`u-text-md`, `u-text-regular`, `u-text-sm` — large to small. `u-text-regular` is the default body size; reach for it unless the design says otherwise.

### Rich text

`u-richtext` on the Webflow Rich Text element. It styles every nested tag (h1–h6, `<p>`, blockquote, ordered/unordered lists, links, bold, emphasis, superscript, subscript). Never add per-tag CSS inside a rich text block — style it once here or the CMS output drifts from the rest of the site.

### Font and weight

`u-font-primary` (Arial) · `u-weight-400` · `u-weight-500` · `u-weight-700`

Use the weight utilities rather than a raw `font-weight: 600` in component CSS — the guide only ships 400/500/700, and any other value falls back to a synthesised weight.

### Color

`u-color-primary` · `u-color-secondary` (text) · `u-bg-dark` · `u-bg-secondary` (background)

When an element gets a background utility, put its text color on the *same* element rather than on a child, so contrast stays correct if the background changes.

### Display and flex/grid

`u-display-none` · `u-display-contents` · `u-display-flex` · `u-display-grid`
`u-flex-wrap` · `u-flex-shrink-0`
`u-align-items-start` · `u-align-items-center` · `u-align-items-end`
`u-justify-content-between` · `u-justify-content-center`
`u-place-items-center` · `u-justify-items-center`

These cover the common cases only. A one-off `grid-template-columns` or `gap` value belongs in the component class. But if a pattern like `flex-direction: column` recurs across builds, it earns a new utility — add it to the Styleguide page first (as a real, styled `u-*` class, e.g. `u-flex-column`), then use it elsewhere as an attribute class. Never apply a `u-*`-named class that hasn't actually been defined on the Styleguide page yet — check there first, and if it's missing, create it there before reaching for it anywhere else.

Icons and logos sitting beside text need `u-flex-shrink-0`, otherwise flex squashes them at narrow widths.

### Position and stacking

`u-position-relative` · `u-position-absolute` · `u-zindex-n1` · `u-zindex-1` · `u-isolation-isolate` · `u-overflow-hidden`

`u-isolation-isolate` creates a new stacking context. Put it on a section or card whose children use `u-zindex-n1` (a decorative background layer) so the negative index stays trapped inside the component instead of sliding behind the page background.

### Text handling

`u-text-uppercase` · `u-text-center` · `u-text-wrap-balance` · `u-text-wrap-pretty` · `u-list-unstyled` · `u-line-clamp` · `u-select-none`

`u-text-wrap-balance` is for headings (evens out line lengths across a few lines); `u-text-wrap-pretty` is for paragraphs (mainly kills orphans). Using balance on long body copy is expensive and does nothing useful.

`u-line-clamp` truncates to 3 lines by default. To change it, set the custom property on the component class — don't touch the utility:

```css
.card_excerpt { --_line-length: 2; }
```

### Sizing and media

`u-width-full` · `u-margin-inline-auto` · `u-ratio-1` · `u-cover`

`u-margin-inline-auto` centers a fixed-width block horizontally (left and right auto margins).

`u-ratio-1` locks a square aspect ratio. `u-cover` makes media fill its box. For a square avatar or logo tile, put `u-ratio-1` on the wrapper and `u-cover` on the image. Logos usually want `object-fit: contain` instead — that's a component-class override.

### Accessibility

`u-sr-only` — visually hides content while keeping it available to screen readers. Use it for labels sighted users don't need: icon-only buttons, "Search" on a magnifier input, the accessible name of a nav toggle.

The Navbar ships a "Skip to Main" link pointing at `#main`, carrying both `class="skip-link"` (Global Styles hides it except when focused: `.skip-link:not(:focus-visible) { opacity: 0; height: 0; }`) and `data-target="skip-link"` (the site-wide Footer Custom Code script listens for this attribute — see "Site-wide JavaScript" — to move focus into the target on click, waiting out any in-progress scroll first). The page's main content wrapper must carry `id="main"` or the skip link dead-ends. Every page needs it.

## Page structure

Every page follows this exact shape — don't restructure it:

```html
<body>
  <div class="page-wrapper">
    <!-- Global Styles component (code embed) sits here as a component instance -->
    <!-- Navbar component -->
    <main id="main">
      <!-- page sections -->
    </main>
    <!-- Footer component -->
  </div>
</body>
```

`.page-wrapper` is the single child of `<body>` and holds everything else. `Global Styles`, `Navbar`, and `Footer` are shared components — check the Components panel before rebuilding any of them. `main` is where page-specific sections go, and it must carry `id="main"` for the skip link.

## Variables

All spacing, sizing, color, and font-size values are Webflow variables (Base mode), not literals. Reach for the variable — never hardcode a hex code or a raw `rem` value that duplicates one of these:

**Font Sizes** (fluid — see below): `display`, `h1`–`h6`, `text-md`, `text-regular`, `text-sm`

**Sizes**: `container` (`1280px`, fixed) · `section-spacing-inline` (fluid) · `section-spacing-block` (fluid)

**Colors**: `primary` (`#03280d`) · `secondary` (`#f2fbd0`) · `white` · `black` · `dark` (`#333`) · `transparent`

**Font Family**: `primary` (Arial)

**Settings** (inputs to every fluid clamp, not used directly): `viewport-max: 90` · `viewport-min: 23.4375`

### Fluid typography

Every font size and the two section-spacing sizes are built with the **Fluid Builder** app as a `clamp()` driven by `Settings/viewport-max` and `Settings/viewport-min`, e.g.:

```css
clamp(4 * 1rem, ((4 - (7 - 4)) / (viewport-max - viewport-min) * viewport-min) * 1rem + ((7 - 4) / (viewport-max - viewport-min)) * 100vw, 7 * 1rem)
```

Never hand-write a `clamp()` for text size or section spacing, and never override a font-size utility with a fixed `rem` value — it breaks the fluid scaling on every viewport between the min and max. If a new fluid value is genuinely needed (a size the guide doesn't have), add it as a new variable via Fluid Builder rather than hardcoding, so it scales the same way as everything else.

## `.container`

`.container` already exists (`1280px` from the `container` size variable) and centers via `margin-inline: auto`. Use it as the max-width wrapper inside a section — don't rebuild it as a component class:

```html
<section class="section-hero u-spacing-block u-spacing-inline">
  <div class="container">
    <div class="hero_layout">
      <div class="hero_content">
        <h1 class="hero_title u-heading-h1 u-text-wrap-balance">Headline</h1>
        <p class="hero_text u-text-regular">Supporting copy.</p>
      </div>
      <div class="hero_visual u-ratio-1 u-overflow-hidden">
        <img class="hero_img u-cover" src="" alt="" />
      </div>
    </div>
  </div>
</section>
```

- `section-*` — the `<section>` element itself. Named `section-[component]` (e.g. `section-hero`, `section-faq`), not `[component]_wrap`. Carries `u-spacing-block u-spacing-inline` (fed by the `section-spacing-*` variables) and any background utility.
- `.container` — the max-width constraint. Never put grid/flex layout on it directly.
- `_layout` — the actual grid or flex arrangement, styled on the component class.

## Section spacing

`u-spacing-inline` (horizontal) and `u-spacing-block` (vertical) read the fluid `section-spacing-inline`/`section-spacing-block` variables. Two combo classes trim one side of `u-spacing-block` so consecutive sections don't double up their padding:

| Combo | Effect | Use when |
|---|---|---|
| `cc-top-0` | Removes top padding, keeps bottom | Section sits directly under a related section |
| `cc-bottom-0` | Removes bottom padding, keeps top | Section flows straight into the next related one |

Two sections that read as one block get `cc-bottom-0` on the first and `cc-top-0` on the second — that collapses the seam to a single gap instead of two stacked ones.

## Component class naming

- `<section>` elements are named `section-[component]` (hyphenated, e.g. `section-hero`, `section-faq`) — this is the one place a hyphen separates the whole name rather than joining a multi-word part.
- Everything inside a section uses underscores: `[component]_[element]` → `hero_title`, `faq-card_description`
- Broadest to most specific, left to right
- Max three underscores; deeper nesting starts a fresh component name
- Hyphens only inside a multi-word part (outside the `section-*` case above)
- Preferred short names: `_title` not `_heading`, `_text` not `_paragraph`, `_img` not `_image`
- Every element gets a component class, even if all its styling comes from utilities — a bare `<div class="u-display-flex">` is unidentifiable in the Navigator and untargetable later

## Global Styles component

A code-embed component named **Global Styles**, placed once inside `.page-wrapper`, holds every global rule. Its current contents already cover:

- Font smoothing and `text-rendering: optimizeLegibility` on `body`
- `box-sizing: border-box` on `::before`/`::after`
- `line-height: inherit` on all headings
- `img, svg, video { display: block; max-width: 100%; }`
- Links inherit color; any classed link (`a[class]`) loses the underline
- All `.u-richtext` child spacing (margins between block children, list-item spacing, first/last-child trim) — **never add your own margin rules for rich text children**, they're already handled here
- `.u-line-clamp` itself — the utility's actual `-webkit-line-clamp` CSS lives in this embed, reading `--_line-length` (default `3`)
- `.faq-card[open] .faq-card_arrow { rotate: -180deg; }`
- The shared `.button` hover/active pattern (see Buttons below)
- Navbar skip-link visibility, mobile-menu scroll lock, and the nav overlay tint

Because this embed already owns these rules, don't re-implement them at the component level — extend the embed itself if a genuinely new global rule is needed, rather than duplicating similar CSS scoped to one component.

## Site-wide JavaScript (Footer Custom Code)

A jQuery-based script is injected site-wide via **Project Settings → Custom Code → Footer Code** — it runs on every page inside `window.Webflow.push(...)`, separately from the Global Styles CSS embed. It already implements the behaviors below; don't re-solve any of them at the component level. Exact code: `references/js-reference.md` § 1.

- **Navbar height** — measures `.navbar` (the Navbar component's root class) and sets `--navbar-height` on `<html>`. Use `var(--navbar-height)` anywhere that needs to account for the navbar's height — sticky offsets, `scroll-margin-top` on anchor targets, full-height sections under a fixed nav.
- **Skip link** — any `[data-target="skip-link"]` link waits for in-progress scrolling to settle, then moves focus to the first focusable element inside its `href` target (or focuses the target itself if nothing inside is focusable).
- **Mobile nav auto-close** — clicking any `.navbar_nav_link` programmatically clicks `.navbar_toggler` if the mobile menu (`w--open`, Webflow's native Navbar interaction class) is open. Nav links in the Navbar component must use exactly this class for the auto-close to fire.
- **Rich text cleanup** — empty leading/trailing `<p>` tags inside `.w-richtext` (Webflow's native Rich Text element class, which `u-richtext` styles alongside) are stripped automatically on load. Don't manually clean these up in markup.
- **Current year** — `[data-target="current-year"]` has its text replaced with the current year on load (see the copyright example above).
- **FAQ accordion** — see FAQ Card below; the smooth open/close and single-open-per-group behavior are both driven by this script, not by CSS or native `<details>` alone.

The project depends on jQuery (`$`) being loaded globally — any additional component JS should assume it's present rather than reimplementing DOM helpers, and shouldn't redefine `$`.

## Reference JS patterns (Tinyflow toolkit)

These aren't necessarily wired into every project already — they're a standing toolkit of patterns used across Tinyflow builds. Reach for them instead of writing an equivalent from scratch when a design calls for what they cover. Exact code: `references/js-reference.md` § 2.

**Helpers**

- `matchMediaQuery(breakpointString, parameterCallback)` — runs `parameterCallback` only if `window.matchMedia(breakpointString).matches`. Use for one-off JS behavior gated to a breakpoint, instead of hand-rolling a `matchMedia` check inline.
- `isSupportHover()` — `window.matchMedia("(hover: hover)").matches`. Check this before wiring up any hover-only interaction in JS, so touch devices don't get a stuck hover state.
- `getFluidSize(minSize, maxSize, currentW, minW = 375, maxW = 1440)` — the JS equivalent of a CSS fluid `clamp()`, for numeric options a library needs in JS rather than CSS (e.g. a Swiper `spaceBetween` value). The default `minW`/`maxW` (375/1440) match the project's `viewport-min`/`viewport-max` Settings variables (`23.4375rem`/`90rem` at a `16px` root) — keep them in sync if those variables ever change.

**Lazy-loaded libraries** — always gated behind a presence check for the library's own marker selector, so pages that don't use a slider never pay for its CSS/JS:

- **Swiper** — loaded only if `.swiper` exists on the page. Individual sliders are picked up by a `data-swiper-slider` value: `data-swiper-slider="team"` (free-scroll, `spaceBetween` sized via `getFluidSize(16, 32)`), `data-swiper-slider="related-project"` (1-up below 768px, 2-up with nav arrows above via `data-swiper-navigation-next/prev="related-project"`).
- **Splide** — loaded only if `[data-splide-slider="marquee"]` exists, with the auto-scroll extension. Configure per-instance with `data-speed` (auto-scroll speed, default `0.5`) and `data-direction="reversed"` on the marquee element itself — no JS edits needed to adjust a single marquee's speed or direction.
- **Lenis** (smooth scroll) — loaded unconditionally, ties into GSAP `ScrollTrigger` if GSAP is present on the page.

**Small utilities**

- **Social share links** — any `[data-share="social"]` anchor gets the current page URL appended to its existing `href`. Build the anchor's `href` as the share service's template ending right before the URL param (e.g. a Twitter/X intent URL ending in `?text=...&url=`), and this appends the rest.
- **AI summarize links** — `[data-ai-summarize]` anchors get a "summarize this article" prompt appended to their template `href` (ChatGPT/Claude-style deep links), plus `rel="nofollow noopener noreferrer"`. Build the anchor's `href` as the target site's query-prefilled URL (e.g. `https://chatgpt.com/?q=`) and the prompt gets appended automatically.
- **Blog CTA injection** — for blog posts, CTA blocks can be author-positioned by percentage-through-the-post rather than a fixed spot in the layout. The rich text container needs `data-element="richtext"`; each CTA element needs `data-blog-component-id` and `data-blog-component-position` (0–100). Exact code: `references/js-reference.md` § 3.

## Buttons

There's a shared `.button` class, not a per-component pattern. It reads two custom properties for its hover/active state, already wired to transition on `prefers-reduced-motion: no-preference`:

```css
.my-cta_button {
  --_hover-color: var(--colors--secondary);
  --_hover-bg: var(--colors--primary);
}
```

Set `--_hover-color` and `--_hover-bg` on the component class to theme a button — don't write your own `:hover`/`:active`/`transition` rules on top of `.button`, the embed already applies them globally. Colors variables generate as `--colors--{name}` (confirmed: `--colors--primary`, `--colors--secondary`) — the same pattern likely holds for `--colors--white`, `--colors--black`, `--colors--dark`, `--colors--transparent`, but confirm any you haven't used yet in the Designer's variable panel rather than assuming.

## FAQ Card

The FAQ component is a native `<details>`/`<summary>` pair, but the accordion behavior itself — smooth open/close and closing other cards in the group — is driven by the site-wide Footer Custom Code script, not by CSS or the native `name` grouping alone. The list is a real `<ul>`/`<li>`, matching the site's general list rule, with `data-faq="list"` on the `<ul>` and `data-faq="listitem"` on each `<li>`:

```html
<ul data-faq="list" role="list" class="faq-list u-list-unstyled">
  <li data-faq="listitem">
    <details name="faq-group" data-faq="card" class="faq-card" open="">
      <summary data-faq="toggler" class="faq-card_header u-justify-content-between u-display-flex">
        <h3 class="faq-card_title u-weight-500">Title goes here 1</h3>
        <div class="faq-card_arrow u-flex-shrink-0 u-display-grid u-place-items-center">🔽</div>
      </summary>
      <div data-faq="body" class="faq-card_body u-overflow-hidden">
        <div class="faq-card_content">
          <p class="faq-card_description">Answer goes here 1</p>
        </div>
      </div>
    </details>
  </li>
  <li data-faq="listitem">
    <!-- next .faq-card, no `open` attribute -->
  </li>
</ul>
```

`role="list"` on the `<ul>` restores the "list" accessibility role that `list-style: none` (inside `u-list-unstyled`) strips in some browsers/screen readers — see the general list rule below, this isn't FAQ-specific.

- `data-faq="list"` on the `<ul>`, `data-faq="listitem"` on each `<li>` — required. The script scopes its "only one card open" logic to cards inside the same `[data-faq="list"]`, and force-opens the first card on load if none in the group already have `[open]` set. Either leave every card closed and let the script pick the first, or set `open=""` on one explicitly (as shown above) — both are fine, just don't assume markup order alone determines the default state.
- `name="faq-group"` on each `<details>` — every card in the group shares this value; the script temporarily removes/restores it on other open cards while animating so its slide animation doesn't fight the native grouping behavior.
- `data-faq="card"` / `"toggler"` / `"body"` — required hooks the script queries directly (`data-faq="toggler"` is the click target, `data-faq="body"` is what gets `slideUp`/`slideDown`). They carry no styling themselves but the FAQ won't function without them.
- `[open]` is the native `<details>` attribute — Global Styles targets `.faq-card[open] .faq-card_arrow` to rotate the arrow.
- No `aria-expanded` or `aria-controls` needed; `<details>`/`<summary>` still gives disclosure semantics and keyboard operation for free even though the click/animation is scripted.

## Shared components

The style guide already contains **Navbar**, **Footer**, **FAQ Card**, and **Style Guide Badge**, plus the **Global Styles** embed and **`.container`**. Before building any of these, check whether the project's Components panel already has it — duplicating a shared component is the most common way these projects drift. Build a new one only when the design genuinely differs, and give it a distinct component name.

The Components panel is organized into categories — **Cards** (FAQ Card), **Embeds** (Global Styles), **Layouts** (Footer, Navbar), and a **Styleguide Elements** group (Style Guide Badge and similar). When creating a new shared component, file it under the matching category (or the closest fit) instead of leaving it uncategorized, so the panel stays browsable as it grows.

Navbar nav links and any other list of items (Navbar menu, Footer link columns, etc.) use native `<ul>`/`<li>` structure — never a stack of `<div>`s standing in for a list. When `u-list-unstyled` is applied, also add `role="list"` to the `<ul>` — removing the bullets (`list-style: none`) strips the implicit "list" accessibility role in some browsers, and `role="list"` restores it:

```html
<ul role="list" class="navbar_menu u-list-unstyled u-display-flex">
  <li class="navbar_menu_item">
    <a href="/" class="navbar_nav_link">Home</a>
  </li>
  <li class="navbar_menu_item">
    <a href="/styleguide" class="navbar_nav_link">Style Guide</a>
  </li>
</ul>
```

`u-list-unstyled` strips the default markers/indent, so use it whenever a `<ul>` is doing layout duty rather than displaying as a visible bulleted list. Nav links specifically must keep the `.navbar_nav_link` class exactly as shown — the site-wide mobile-nav auto-close script (see "Site-wide JavaScript" below) targets that class by name.

The Footer's copyright year is a live element, not a hardcoded string. The `<span>`/text node carrying the year needs `data-target="current-year"` — something reads that attribute to keep the year current:

```html
<div class="footer_copyright">
  Copyright © <span data-target="current-year">2025</span> Tinyflow
</div>
```

## Rules

- Write CSS for component classes only. Never write a rule whose selector is a `u-*` or `cc-*` class alone.
- Class-only selectors. No IDs, tag selectors, or descendant chains — Webflow's class model doesn't survive them.
- No inline `style=""`.
- No `!important`. If a utility is being overridden, the component class is fighting the system — restructure instead.
- `rem` for sizing and spacing, `ch` for text `max-width`, `em` for anything that should scale with its own font size. Avoid `px`. Never hand-write a fluid `clamp()` — use a Fluid Builder variable.
- A combo class must appear in the published HTML or Webflow purges it. If `cc-*` is only ever applied by JavaScript, park one element carrying it inside a `[component]_hidden u-display-none` div.
- Set `alt=""` on decorative images and `aria-hidden="true"` on decorative SVGs.
- Component and combo classes attach via Webflow's native class-selector panel; `u-*` utility classes attach as a custom `class` attribute instead (see "How classes attach to elements" above).
- Use variables (color, font size, spacing) instead of literals — check the Variables panel before hardcoding a value that might already exist as one.
- Any list of items (nav menu, footer links, card grids repeated from the same data, etc.) is a `<ul>`/`<li>` — never `<div>`s stacked to look like a list. Pair `u-list-unstyled` with `role="list"` on the same `<ul>`.
- The Footer's copyright year element carries `data-target="current-year"`.
- The skip link carries both `class="skip-link"` and `data-target="skip-link"`; Navbar nav links carry `class="navbar_nav_link"`; every FAQ group's wrapper carries `data-faq="list"` — these attributes are read by the site-wide Footer Custom Code script and the feature silently breaks without them.

## Anti-patterns

- Redefining or extending an existing `u-*` class
- A `cc-*` class used alone, unscoped, or on an element without its base class
- Applying a `u-*`-named class that was never actually created on the Styleguide page — a new recurring pattern (e.g. `u-flex-column`) gets added there first, then used elsewhere; skipping that step ships an unstyled class
- Attaching a `u-*` utility through the native class-selector panel instead of a custom `class` attribute
- Writing `sr-only` — the class is `u-sr-only`, with the prefix
- Writing `u-margin-block-auto` — the class is `u-margin-inline-auto`
- Raw `font-weight` values outside 400/500/700
- Per-tag CSS inside a `u-richtext` block, or any custom richtext spacing — it's already in Global Styles
- Hand-written `:hover`/`:active`/`transition` CSS on a `.button` — set `--_hover-color`/`--_hover-bg` instead
- An accordion/FAQ built with custom ARIA-attribute JS instead of the `<details>`/`<summary>` + `data-faq="list"`/`"card"`/`"toggler"`/`"body"` pattern the site-wide script expects
- A FAQ card or group missing `data-faq="list"`/`role="list"` on the `<ul>`, `data-faq="listitem"` on each `<li>`, or `data-faq="card"`/`"toggler"`/`"body"` on the card's parts — the accordion silently won't animate or group
- A skip link without `data-target="skip-link"`, or a Navbar link that isn't `.navbar_nav_link` — the site-wide behaviors won't fire
- Reimplementing navbar-height measurement, mobile-nav auto-close, rich-text empty-paragraph cleanup, or current-year injection at the component level instead of relying on the site-wide Footer Custom Code script
- Loading Swiper, Splide, or another slider library unconditionally instead of gating it behind a presence check for its own marker selector (`.swiper`, `[data-splide-slider="marquee"]`)
- Hand-rolling a fluid-size or hover-support check in new component JS instead of using `getFluidSize()` / `isSupportHover()`
- A blog CTA block without `data-blog-component-id` + `data-blog-component-position`, or a rich text container missing `data-element="richtext"` — the position-based CTA injection silently skips them
- Nav menus, link lists, or repeated-item groups built from `<div>`s instead of `<ul>`/`<li>`
- `u-list-unstyled` applied without `role="list"` on the same `<ul>`
- A Footer copyright year hardcoded without `data-target="current-year"`
- Hardcoded colors or hex codes instead of a Colors variable
- Hardcoded `rem` text sizes or a hand-written `clamp()` instead of a fluid Font Sizes / Sizes variable
- Layout CSS on `.container` — use a `_layout` child
- Stacked `u-spacing-block` sections without `cc-top-0` / `cc-bottom-0` where they read as one block
- Anything outside `.page-wrapper`, or a page missing `id="main"` (breaks the Navbar's skip link)
- Elements with only utility classes and no component class
