# Handoff: Connetic Post Builder

## Overview
The **Post Builder** is an internal tool for a marketing/agency team that drafts, edits, and ships LinkedIn-style thought-leadership posts on behalf of a client founder ("Brad Zapp", President & Fund Manager at Connetic RIA LLC). Each post is shown as a realistic LinkedIn post preview (avatar, name, body copy, optional generated graphic, reaction counts). The operator works **one month at a time**, building toward a target of ~12 posts/month, then exports the batch or pushes it to HubSpot / sends it to the client for review.

The companion **Graphic editor** lets the operator attach a branded graphic to any post (templated quote cards, headline cards, photo quotes, etc.) in Connetic brand colors.

## About the Design Files
The files in this bundle are **design references created in HTML** — interactive prototypes that demonstrate the intended look, layout, and behavior. They are **not production code to copy directly**. They are written as "Design Components" (a custom `.dc.html` format with a small bespoke runtime, `support.js`) that is specific to the design tool they were built in.

The task is to **recreate these designs in the target codebase's existing environment** (e.g. React, Vue, Svelte, etc.) using its established component library, state-management, and styling conventions. If no app environment exists yet, choose the most appropriate modern framework (React + TypeScript is a safe default) and implement there. Treat the HTML/CSS/JS as the spec for *appearance and behavior*, not as files to ship.

## Fidelity
**High-fidelity (hifi).** Colors, typography, spacing, radii, shadows, and interactions are all final and intentional. Recreate the UI pixel-accurately using the target codebase's libraries. Exact hex values, font families, and sizes are listed below in **Design Tokens**.

---

## Screens / Views

The app is a single full-viewport surface (`height:100vh; min-height:680px`) with one primary view (the **Gallery**) and three overlay modals (**Founder editor** popover, **Brand templates** modal, **Graphic editor** modal). A transient **send/export status** modal also appears during export/HubSpot/review actions.

### 1. Gallery (primary view)
**Purpose:** Browse and edit all posts for a single month; add/remove posts; expand a post to edit its copy, footer CTA, and graphic.

**Layout (top to bottom, flex column, full height):**
1. **Header bar** — white, `padding:12px clamp(14px,3vw,30px)`, `border-bottom:1px solid #e3e2df`, space-between, wraps on narrow widths.
   - **Left:** Founder identity button — 44×44 circular avatar + text block. Eyebrow `POST BUILDER · CONNETIC` (10.5px, 700, `letter-spacing:0.1em`, uppercase, `#b0b0ab`). Name "Brad Zapp" (17px, 800, `#1d2226`) with a small edit (pencil) icon. Title line (12px, `#8a8a86`). Clicking opens the **Founder editor** popover (below).
   - **Right:** action cluster (`gap:10px`):
     - **Download** dropdown button — bg `#F2E9D5`, border `#E2D4B4`, text `#4a4332`, `box-shadow:0 2px 8px rgba(120,100,60,.30)`. Opens a 208px menu with two items: "All posts as PDF" (red doc icon, sub "Visual, one page each") and "All text as Word" (blue doc icon, sub "Editable .doc copy").
     - **Send to client for review** — same beige pill style, eye icon (`#1e4a85`).
     - **Send to HubSpot** — solid HubSpot orange `#FF7A59`, white text, 700, send icon.
2. **Month selector bar** — white, `padding:11px clamp(14px,3vw,24px)`, `border-bottom:1px solid #ececea`.
   - Two leading pill buttons: **All** and **This month** (bg `#faf9f7`, border `#e0dfdb`, radius 999px, 12.5px/700).
   - A 1px `#e6e5e1` divider.
   - A horizontally-scrolling row of 12 **month chips** (Jan…Dec). Each chip: `padding:8px 14px`, radius 999px, 13px/600. Selected chip = bg/border `#4F46E5`, white text; unselected = white bg, `#e0dfdb` border, `#3b3b38` text. Chips with posts show a count badge (selected: `rgba(255,255,255,0.25)` bg / white; unselected: `#eceaff` bg / `#4F46E5` text).
3. **Scroll area** — `flex:1; overflow-y:auto; padding:24px clamp(14px,3vw,32px) 50px`. Renders one **month section** per selected month.

**Month section:**
- Header row (space-between, wraps): `<h2>` "{Month} {Year}" (18px, 800) + count label ("N posts", 13px `#9a9a95`) on the left; on the right a "Posts this month" label, a **stepper** (− / count / +, bordered, each button 34×34), and a "target 12" hint (`#bdbdb8`).
- Empty state: dashed `#d6d5d1` border, radius 11px, centered helper text on `#fbfbfa`.
- **Card grid:** `display:grid; grid-template-columns:repeat(auto-fill, minmax(338px, 1fr)); gap:22px`.

**Post card** (one per post + a trailing "Add post" tile):
- Above each card: a row with "Post #N" label (12.5px, 700, `#6a6a66`) and a delete button (26×26, red trash icon, revealed on card hover).
- Card: white, `border:1px solid` (`#e0dfdb` normal / `#4F46E5` when expanded), radius 11px, subtle shadow; `:hover` lifts `translateY(-3px)` with a stronger shadow. Structure:
  - **Author row** (`padding:14px 16px 0`): 48×48 avatar + name (14px/700) + LinkedIn verified badge (inline SVG, `#0A66C2`) + "· 1st"; title line (12px, ellipsis); time + "🌐" line (11.5px).
  - **Body:** collapsed = clickable text block (13.5px, line-height 1.45, `white-space:pre-wrap`; placeholder "Tap to write Brad's post…" in `#a0a09b`). Clicking expands to an inline editor: a `<textarea>` (6 rows) + char count + three buttons: **Generate** (navy `#1e4a85`, sparkle icon), **Cancel** (red `#fdecec`/`#f3b4b4`/`#b4332b`), **Save** (green `#e7f7ee`/`#b6e3cb`/`#1f8a5b`). Supports paste-from-Word cleanup (strips formatting, keeps emoji).
  - **Footer CTA:** when set, shows the chosen CTA text block. When expanded, shows a "FOOTER" label and a list of selectable options ("None", "Practice CTA", "Advisor DM", "Book a call"), each a bordered row; selected = `#1e4a85` border on `#eef2fb`.
  - **Media/graphic:** full-width block, `aspect-ratio` from graphic size (square `1/1`, banner `1.91/1`, tall `3/4`). If no graphic, shows a placeholder gradient + a monospace filename. If a graphic exists, renders the generated graphic HTML. Hover overlay (`rgba(14,34,54,0.55)`) reveals an "Edit graphic" pill → opens **Graphic editor**.
  - **Engagement row** (`padding:9px 16px 12px`): overlapping reaction emoji bubbles (👍 on `#0A66C2`, ❤️ on `#df704d`, 💡 on `#f5bb5c`, each 17px, white 1.5px border, `-5px` overlap) + formatted like count; right side "N comments · N reposts".
- **Add post tile:** dashed `#cdccc8` border, `min-height:230px`, `#fbfbfa` bg, centered "+" icon + "Add post"; `:hover` turns `#4F46E5`.

### 2. Founder editor (popover)
**Purpose:** Change the founder avatar (file upload / remove) and the title line that applies to every post.
- Anchored under the header identity button: 312px, white, `border:1px solid #e3e2df`, radius 13px, `box-shadow:0 14px 34px rgba(0,0,0,0.18)`, `padding:18px`, `z-index:45`. A full-screen transparent click-catcher (`z-index:35`) closes it.
- "EDIT FOUNDER" label; 56×56 avatar with hover camera overlay + hidden `<input type=file>`; "Remove photo" button (red text); a "Title (applies to every post)" `<textarea>` (2 rows); navy "Done" button.

### 3. Brand templates (modal)
**Purpose:** Pick a pre-made brand graphic template to start from.
- Overlay `rgba(14,34,54,0.55)`, centered card 820px × 86vh, radius 16px. Header has a navy→teal gradient icon tile (grid icon, `#8CC63F` stroke), title "Brand templates", subtitle "Connetic founder library · pick one to edit for this post", and an × close button.
- Body: scrollable grid `repeat(auto-fill, minmax(180px,1fr))`, gap 14px, of template thumbnails (rendered via the graphic renderer). Clicking a thumbnail applies that template to the current post.

### 4. Graphic editor (modal)
**Purpose:** Configure the graphic attached to a post.
- Overlay `rgba(14,34,54,0.5)`, centered card 980px × 88vh, radius 16px. Header: navy→teal gradient pencil-icon tile, "Graphic editor" + context label.
- Controls (left) drive a live preview (right):
  - **Template** chips — 13 templates: Statement, Big statement, Headline, Spotlight, Center quote, Two-part, Arrow point, Numbered list, Arrow list, Social card, Photo quote, Spokesperson, Photo. Selected chip = `#1e4a85` bg / white.
  - **Color variant** swatches — Navy `#1e4a85`, Teal `#22867c`, Green `#2e9e62`, Ink `#0e2236` (34×34, selected = `#1d2226` 2px border).
  - **Size** segmented control — Square / Banner / Tall.
  - Text fields (eyebrow / body) where applicable; an "auto text from post body" behavior derives card text from the post copy (strips emoji, collapses whitespace) unless overridden.
  - Image picker for photo-based templates (Photo quote / Spokesperson / Photo) from the asset library.
  - Footer buttons: Remove, Cancel/Close, Apply.

### 5. Send / Export status (transient modal)
Shown while exporting PDF/Word, sending to HubSpot, or sending for review: a small centered card with a spinner (`@keyframes pmcspin`) transitioning to a "done" state with a count of posts processed.

---

## Interactions & Behavior

- **Month selection (single-month model):** The gallery shows **one month at a time**. Clicking any month chip selects *only* that month (`selectedMonths = [idx]`). "This month" jumps to the current month. "All" expands to show every month's section stacked. On load the app **always defaults to the single current month** regardless of any persisted selection.
- **Stepper** (per month): − / + adjust the number of posts in that month. Increasing adds generated default posts; decreasing trims from the end. Target is 12.
- **Add post:** appends a blank post to that month and (typically) expands it for editing.
- **Delete post:** removes a post (delete button revealed on card hover); `e.stopPropagation()` so it doesn't expand the card.
- **Expand/collapse:** clicking a collapsed card body expands inline editing; only one card is expanded at a time (`expandedIndex`). Collapse via Cancel/Save.
- **Generate text:** a "Generate" affordance produces post copy (in the prototype it pulls from a curated content pool; in production wire this to your LLM/content service). Button shows a busy label while generating.
- **Paste from Word:** `onPaste` handler cleans pasted rich text to plain text while preserving emoji.
- **Footer CTA:** selecting an option sets `post.footer` (index into the CTA list) or `null` for None.
- **Graphic:** clicking the media area opens the Graphic editor; "auto text" re-derives the card text from the post body whenever the body changes (unless the user has overridden it).
- **Avatar upload:** file input reads an image (as a data URL in the prototype) and sets it as the founder photo; "Remove photo" reverts to a color avatar (`photoColor`).
- **Exports / sends:** Download → PDF (visual, one page per post) or Word (editable text). "Send to client for review" and "Send to HubSpot" each open the transient status modal, then resolve to a done state.

**Transitions/animations:**
- Card hover: `transform .14s, box-shadow .14s` → `translateY(-3px)` + `0 12px 28px rgba(0,0,0,0.13)`.
- Swatch hover: `transform: scale(1.08)`.
- Overlays (edit-graphic pill, avatar camera, delete/edit pills): `opacity 0 → 1` on hover, `.15s`.
- Spinner: `@keyframes pmcspin { to { transform: rotate(360deg); } }`.
- Input focus: border `#4F46E5`, `box-shadow:0 0 0 3px rgba(79,70,229,0.14)`.

**Responsive:** Header and section header rows wrap (`flex-wrap:wrap`). Card grid is `auto-fill minmax(338px, 1fr)`. Horizontal scroll on the month-chip row. Page paddings use `clamp(14px,3vw,…)`.

---

## State Management

Top-level state (from the prototype's logic class):
- `view` — `"gallery"` (only primary view; modals are separate booleans).
- `selectedMonths` — array of month indices currently shown. **Single-month by default** (`[currentMonth]`); "All" sets all 12.
- `posts` — array of post objects (see shape below). Source of truth for every month's content.
- `editIndex` / `expandedIndex` — which post is being edited / inline-expanded (`null` = none).
- `photo` (avatar src or data URL), `photoColor` (fallback avatar color), `bradTitle` (founder title string).
- `modal` — `null` or `{ stage: 'sending'|'done', count }` for the transient export/send modal.
- `gfx` — `null` or `{ postIdx, draft, existing }` for the Graphic editor (draft is the in-progress graphic config).
- `brandOpen`, `dlOpen`, `bradOpen`, `genBusy`, `linkOpen`, `linkDraft` — overlay/UI flags.

**Post object shape:**
```
{
  monthIndex,            // 0–11
  body,                  // post copy (string, may contain emoji + newlines)
  degree,                // "1st" connection label
  time,                  // relative time string e.g. "2h"
  verified,              // bool → LinkedIn verified badge
  hasMedia,              // bool → render media block
  image,                 // optional image ref
  graphic,               // null OR { template, variant, size, text, eyebrow, image, autoText }
  footer,                // null OR index into FOOTERS
  linkedProject,         // optional URL
  likes, comments, reposts  // engagement strings (formatted via fmt())
}
```

**Persistence:** The prototype persists a subset to `localStorage` (`posts, photo, photoColor, bradTitle, selectedMonths`) under a versioned key. **Important:** on load, `selectedMonths` is intentionally overridden to the current month so the default is always a single-month gallery. In production, persist to your backend per user/client; keep the "default to current month on load" rule.

**Data fetching (production):** Replace the in-memory content pool and `makeDefaults()` generator with real API calls for: loading a client's posts, generating copy (LLM), generating/saving graphics, and the export/HubSpot/review actions.

---

## Design Tokens

**Colors**
- App background: `#EEEDEA`
- Surface / cards / bars: `#ffffff`
- Primary text / ink: `#1d2226`
- Secondary text: `#5e5e5a`
- Muted text: `#8a8a86`, `#9a9a95`, `#a0a09b`
- Faint text / hints: `#b0b0ab`, `#bdbdb8`
- Hairline borders: `#e3e2df`, `#ececea`, `#e0dfdb`, `#dcdbd7`
- Selection accent (indigo): `#4F46E5` (focus ring `rgba(79,70,229,0.14)`, soft bg `#eceaff` / `#f6f6fb`)
- Brand navy: `#1e4a85` (soft bg `#eef2fb`)
- Brand teal: `#22867c` · green: `#2e9e62` · ink: `#0e2236`
- LinkedIn blue: `#0A66C2`
- HubSpot orange: `#FF7A59`
- Beige action pill: bg `#F2E9D5`, border `#E2D4B4`, text `#4a4332`
- Success (save): bg `#e7f7ee`, border `#b6e3cb`, text `#1f8a5b`
- Danger (delete/cancel): bg `#fdecec`, border `#f3b4b4`, text `#b4332b` / `#c0392b`
- Hover surfaces: `#f3f2ef`, `#f0efec`, `#faf9f7`, `#fbfbfa`
- Reaction bubble colors: `#0A66C2` (like), `#df704d` (heart), `#f5bb5c` (idea)
- Placeholder graphic gradient: `linear-gradient(135deg,#0e2236,#1e6f7e)`
- Modal scrim: `rgba(14,34,54,0.55)` / `rgba(14,34,54,0.5)`
- Founder avatar swatch options: `#1e4a85, #22867c, #2e9e62, #0A66C2, #0e2236, #E0A93B`

**Typography**
- UI font: system stack — `-apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif`, antialiased.
- Display/graphic fonts (loaded from Google Fonts, used inside generated graphics): **Poppins** (500/600/700) and **Roboto** (700/800/900).
- Key sizes: eyebrow 10.5px/700; section `<h2>` 18px/800; founder name 17px/800; card name 14px/700; body 13.5px (line-height 1.45–1.5); labels 11–12.5px; counts 11.5–13px. Letter-spacing: eyebrows `0.1em`, headings `-0.01em`.

**Spacing / sizing**
- Page padding: `clamp(14px,3vw,30/32px)`; bars `11–12px` vertical.
- Card grid gap 22px; card radius 11px; section bottom margin 34px.
- Avatars: 44px (header), 48px (card), 56px (popover).
- Stepper / icon buttons: 34×34; small icon buttons 26×26.
- Pills/chips radius 999px; inputs radius 8px; modals radius 16px; popover radius 13px.

**Border radius scale:** 7px (small buttons), 8px (inputs/options), 9px (action pills/steppers), 11px (cards), 13px (popover), 16px (modals), 999px (chips/pills).

**Shadows**
- Card rest: `0 1px 2px rgba(0,0,0,0.05)`; card hover: `0 12px 28px rgba(0,0,0,0.13)`.
- Action pills: `0 2px 8px rgba(120,100,60,.30)` (beige), `0 2px 8px rgba(255,122,89,.35)` (HubSpot), `0 2px 8px rgba(30,74,133,.32)` (navy).
- Dropdowns: `0 12px 30px rgba(0,0,0,0.16)`; popover `0 14px 34px rgba(0,0,0,0.18)`; modals `0 24px 60px rgba(0,0,0,0.32)`.

---

## Assets
All in `assets/` (copied into this bundle):
- `brad.jpg` — founder headshot (default avatar).
- `connetic-logo.svg`, `logo-black.png`, `logo-white.png`, `advisorpedia.png` — brand marks.
- `img/` — content imagery: `connetic-network.png`, `connetic-tech.png`, `wendal.jpg`, `brad-futureproof.jpg`, `brad-balcony.jpg`, `architecture.jpg`, `coastal.jpg`, `misty.jpg`.
- `founders/photo_01…24` — founder/portrait photo library used by graphics.

Icons are all **inline SVG** (LinkedIn badge, download, send, eye, pencil, trash, plus, sparkle, camera, grid, image). Reproduce with your icon library where equivalents exist; keep the custom LinkedIn verified badge as-is. In a real Connetic codebase, prefer the existing brand asset system over copying these files.

---

## Files
Design reference files (in this bundle):
- `Post Builder.dc.html` — the entire app: gallery, founder editor, brand-templates modal, graphic editor, exports, and all logic (state, defaults/content pool, graphic renderer `gfxHtml`, formatting helpers).
- `Graphic.dc.html` — standalone graphic-template renderer (the same templates used inside Post Builder's media blocks); useful as a clean reference for the graphic system.
- `support.js` — the design-tool runtime that powers the `.dc.html` files. **Do not port this**; it is only here so the prototypes open and run for reference.
- `assets/` — all images/logos referenced above.

> To view the prototypes as intended, open the `.dc.html` files in the original design tool. Outside it, read them as annotated source: the markup is the layout/styling spec, and the `class Component extends DCLogic { … }` block is the behavior/state spec.
