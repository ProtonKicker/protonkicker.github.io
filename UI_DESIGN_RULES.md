# UI Design Rules

Design rules for this site's bento-grid homepage. Follow these when adding or
editing cards, and check the "Testing" section before shipping.

**Core principle:** cards are their natural content height. Empty space belongs
to the page background, never inside a card and never as an extra gap between
stacked cards.

## 1. Cards stay slim

- A card's height comes from its content. Never stretch a card beyond its
  content, and never add internal whitespace to make it "fill" a row or column.
- When a card spans multiple grid rows and would be stretched by its
  neighbours, give it `align-self: start` so it keeps its natural height
  (see `.span-audiocat` in the tablet breakpoint).
- Known exception: the UC Berkeley / CalSol row is pinned with
  `min-height: 100px` on `.span-berkeley`. This is deliberate — it keeps the
  row height that existed before the "Student" label was removed. Don't remove
  it without meaning to change that row's height.

## 2. Bottoms do not need to align

- Column bottoms are allowed to be ragged. Leftover vertical space simply sits
  on the page background below the last card of the shorter column.
- Do not pad cards, add spacers, or stretch cards to make the bottom edges of
  two columns meet.

## 3. Standard gaps only — no black space between cards

- Cards stacked in a column are separated by the layout's standard gap only:
  18px desktop, 16px tablet, 14px mobile. Nothing else.
- If the space between two stacked cards looks larger than the standard gap,
  it is a layout bug (usually a grid row being stretched by a spanning item).
  Fix the structure — e.g. group the stacked cards in one flex column — rather
  than compensating with margins.
- Reference implementation: `.side-pair` wraps Kocoa Beam + Klipper for Mac.
  It is `display: contents` on desktop and mobile (invisible to the grid) and
  becomes a `flex-direction: column; gap: 16px` stack in the tablet
  breakpoint's right column.

## 4. Text and content spacing

- Keep line-height ratios consistent across equivalent text styles:
  `.info-sub` and `.project-desc` both use `1.4`.
- Info cards (Berkeley, CalSol) vertically center their content
  (`align-items: center` on `.info-row`). When a line is removed, the
  remaining lines re-center and sit slightly higher — that is expected.
- One idea per line: first major + cluster on one line
  (`Applied Mathematics · Fluid Mechanics`), the second major on its own line
  beneath (`Mechanical Engineering`).
- Don't add label lines (like "Student") that repeat information the card
  already conveys.

## 5. Layout structure

- **Desktop (>1024px):** 12-column grid with explicit `grid-column` /
  `grid-row` per card. `grid-auto-rows: minmax(60px, auto)`.
- **Tablet (≤1024px):** 8-column grid, auto rows. Audio Cat occupies the left
  half spanning two rows; `.side-pair` (Kocoa Beam + Klipper for Mac) stacks
  in the right half. Cards keep natural heights.
- **Mobile (≤720px):** single column, cards in DOM order.
- When a card spans rows, the span defines that card's own height only — it
  must not stretch its neighbours (rule 1) or push extra space between them
  (rule 3).

## 6. Testing before shipping

Sweep the width range and check every card at each size, including the
breakpoint boundaries (720/721 and 1024/1025) plus a short-wide window
(e.g. 1280×620):

- **No internal slack** — the gap between a card's last content element and
  its bottom padding edge should be ~0.
- **No clipped content** — `scrollHeight === clientHeight` on `.card-inner`;
  the "View on GitHub" links must always be fully visible.
- **No horizontal overflow** — `document.scrollWidth` must equal the viewport
  width.

Wait for the entrance animation to finish (≥1.2s) before measuring, or values
will reflect the animation's first frame (`scale(0.985)`).
