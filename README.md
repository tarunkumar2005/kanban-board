# Kanban Board

A small drag-and-drop kanban board built with plain HTML, CSS, and JavaScript. Move cards between three columns — To Do, In Progress, and Done — and reorder them precisely within any column using midpoint-based position detection. No frameworks, no build step, no dependencies.

Built as the third project in a 15-project revision series — refreshing HTML/CSS/JS fundamentals by writing every line independently.

## What it does

- Three columns: To Do, In Progress, Done
- Drag any card between columns
- Drop at a precise position within a column using mouse-midpoint comparison
- Visual feedback on drag (column highlight, card lift, cursor change)
- Responsive layout: columns stack vertically on mobile

## How to run it

No installation. No `npm install`. Just:

1. Clone or download the repo
2. Open `index.html` in any modern browser
3. Drag cards between columns

That's it.

## Project structure

```
kanban-board/
├── index.html      # Markup with three lists and static cards
├── style.css       # Styling, card cosmetics, responsive breakpoint
└── script.js       # Drag-drop logic and midpoint reordering
```

The whole thing is roughly 120 lines across three files.

## What I practised building this

- **HTML5 Drag and Drop API** — `dragstart`, `dragover`, `dragenter`, `dragleave`, `drop` events and `dataTransfer` for element identity across drag boundaries
- **DOM node movement** — `appendChild` moves a node between parents automatically; no special `removeChild` is needed, because a DOM node can only have one parent at a time
- **Midpoint comparison for positioning** — `getBoundingClientRect()` plus `clientY` to find which gap a card was dropped into
- **Event delegation vs direct listeners** — attaching per-element `dragstart`/`dragend` and per-container `drop`/`dragover` listeners
- **CSS transitions** — hover lift on cards, `.over` class for column drag feedback
- **Responsive design** — `flex-direction: column` on narrow screens via media query
- **Edge-case handling** — filtering the dragged card out of the `querySelectorAll` result so it is not compared against itself during reordering

## Things I learned the hard way

- **A DOM node has only one parent.** Calling `appendChild(card)` on a different list automatically removes it from the previous one. No `removeChild` is needed. This is different from `cloneNode` which creates a copy.
- **Event listener wiring matters.** `list.addEventListener("dragenter", dragLeave)` compiles and runs but does the wrong thing. Catching this required actively stopping and reading the wiring against the function names. The compiler does not check semantic correctness.
- **Event order during drag.** `dragenter` fires when a dragged item enters a zone; `dragleave` fires when it leaves. If the wrong handler is wiredHandlers, the visual highlight never appears because it is immediately toggled off by the last handle
- **`insertBefore` and `appendChild` are siblings with different use cases.** `appendChild` always appends to the end. `insertBefore(new, ref)` inserts before a sibling, which is the right tool when you know where the card should go. Picking between them based on the comparison result is the pattern that gives exact reordering.
- **Midpoint comparison, not cursor overlap.** At first it felt correct to compare `clientY` against `card.top`, but that makes every drop go to the top of the first card. Comparing against `(top + bottom) / 2` — the vertical midpoint — correctly decides *before* or *after* each card and gives natural drag-and-drop feel.
- **Shadow variable names are silent bugs.** Reusing `card` as a callback parameter silently shadows an outer `const` of the same name. The code still works because the inner `card` is never confused with the outer `card` in this specific case, but it took a review to spot that the two names refer to different things.
- **Redundant spreading is harmless but misleading.** `[...someArray].find()` when `someArray` is already an array from a previous spread. It does not break, but it suggests the spread is doing something meaningful when it is not.

## License

MIT — do whatever you want with it.
