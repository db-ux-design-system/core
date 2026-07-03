# DB UX Design System – Component Page Layout


This skill helps organize and clean up component pages in the DB UX Core Components Figma library. It handles the positioning, naming, and grouping of Component Sets, Sections, and Frames on a component page.

## Overview

Each component page in the library follows a consistent layout structure. This skill ensures that after adding new variants, refactoring, or creating overviews, the page is well-organized and navigable.

## Page Structure (Left to Right)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  Overviews          │  Main Component Sets    │  Subcomponents  │  Deprecated │
│  (Component.Overview│  (Sections/Frames)      │  (Section)      │  (Section)  │
│  instances)         │                         │                 │             │
└─────────────────────────────────────────────────────────────────────────────┘
y = 0 (top-aligned)
```

### Column Order

| Order | Content | Description |
|-------|---------|-------------|
| 1 | Component.Overview instances | Documentation (see `db-ux-component-overview` skill) |
| 2 | Main Component Sets | The primary component variants (Sections like "Desktop", "Mobile") |
| 3 | ↳ Subcomponents | Section containing sub-component sets (e.g. Header, Footer) |
| 4 | Component Sets / Standalone Components | Ungrouped component sets or standalone components not in a section |
| 5 | 🛟 Helpercomponents | Section containing internal helper components |
| 6 | ❌ Deprecated | Section containing old/deprecated component sets (always last) |

### Spacing Rules

| Between | Gap |
|---------|-----|
| Overviews (horizontal) | 100px |
| Last Overview → First Component Section | 100px |
| Sections (horizontal) | 100px |
| Component Sets within a Section (vertical) | Section internal padding |
| Component Sets within a Section (horizontal) | Section internal padding |

## Section Naming Conventions

| Pattern | Usage | Example |
|---------|-------|---------|
| Plain name | Main component direction/variant sections | "Desktop", "Mobile" |
| `↳ Subcomponents` | Contains sub-component sets | "↳ Subcomponents" |
| `❌ deprecated` | Contains deprecated/old component sets | "❌ deprecated" |

## Component Set Naming

> Naming conventions are defined in `db-ux-design-library` § 1. Key patterns for page layout context:

| Pattern | Usage | Example |
|---------|-------|---------|
| `ComponentName → Variant` | Main component with direction/type | "Drawer → (Def) Right", "SHELL → Desktop (Beta)" |
| `↳ ComponentName SUBPART/Variant` | Sub-component | "↳ Drawer HEADER/(Def) Medium", "↳ Shell CONTENT" |
| `❌ ComponentName → Variant (Deprecated)` | Deprecated set | "❌ Drawer → (Def) Right (Deprecated)" |

## Frame/Section Internal Layout

### Sections containing multiple Component Sets

- **Padding:** 80px on all 4 sides
- **Gap between Component Sets:** 100px (both horizontal and vertical)
- **Layout logic:** Position children in a grid-like arrangement starting at (80, 80), with 100px gaps between rows and columns
- **Resize to fit:** After positioning all children, resize the Section to fit its content: `width = rightmost child edge + 80`, `height = bottommost child edge + 80`

**Implementation:**
```js
// Position children inside a section (vertical stacking by default)
function layoutSection(section) {
  const padding = 80;
  const gap = 100;
  const children = section.children;
  
  // Stack vertically
  let currentY = padding;
  
  for (const child of children) {
    child.x = padding;
    child.y = currentY;
    currentY += child.height + gap;
  }
  
  // Resize section to fit
  const maxRight = Math.max(...children.map(c => c.x + c.width));
  const maxBottom = Math.max(...children.map(c => c.y + c.height));
  section.resizeWithoutConstraints(maxRight + padding, maxBottom + padding);
}
```

### Vertical stacking (single column)

When Component Sets are stacked vertically (e.g. multiple directions of the same component):
- Start at (80, 80)
- Each set below the previous with 100px vertical gap
- Section width = widest child + 2 × 80

### Grid layout (grouped columns)

When children of a Section share a common **name prefix** (e.g. `Horizontal/...`, `Vertical/...`, `Tree/...`, `Flat Icon/...`), arrange them as a **column grid** grouped by prefix:

- Each group becomes a column
- Items within a group are stacked vertically (100px gap)
- Columns are placed left-to-right (100px gap between columns)
- All columns start at y = padding (80px)

**Detection:** If child names contain `/` separators where the first segment groups them logically, use grid layout.

**Implementation:**
```js
function layoutSectionAsGrid(section) {
  const padding = 80;
  const gap = 100;
  const children = [...section.children];
  
  // Group by first path segment
  const groups = {};
  for (const child of children) {
    const prefix = child.name.split("/")[0];
    if (!groups[prefix]) groups[prefix] = [];
    groups[prefix].push(child);
  }
  
  // Layout columns
  let columnX = padding;
  for (const [groupName, items] of Object.entries(groups)) {
    let currentY = padding;
    let columnWidth = 0;
    
    for (const item of items) {
      item.x = columnX;
      item.y = currentY;
      currentY += item.height + gap;
      columnWidth = Math.max(columnWidth, item.width);
    }
    columnX += columnWidth + gap;
  }
  
  // Resize to fit
  const maxRight = Math.max(...children.map(c => c.x + c.width));
  const maxBottom = Math.max(...children.map(c => c.y + c.height));
  section.resizeWithoutConstraints(maxRight + padding, maxBottom + padding);
}
```

### Single Component Set (no Section wrapper)

Standalone Component Sets (not wrapped in a Section) are placed directly at the appropriate x position, top-aligned.

## Workflow

### Input Required

1. **Component page URL or name** — which page to organize
2. **File key** — which Figma file

### Steps

1. **Analyze** — Read the page structure, identify all Component Sets, Sections, Frames, and Overview instances
2. **Propose layout** — Present the proposed organization:
   - Which elements go in which column
   - Naming corrections if needed
   - Deprecated items to move
3. **Execute** — After user confirmation:
   - Move Overviews to x=0, y=0 (left)
   - Move main Component Sets/Sections to the right of Overviews
   - Move Subcomponents section after main sets
   - Move Deprecated section to the far right
   - Apply consistent y=0 top-alignment
   - Ensure consistent gaps (100px)

### Rules

| Rule | Details |
|------|---------|
| Top-align everything | All top-level elements (Overviews, Sections, Component Sets) start at y=0 |
| Preserve internal layout | Don't rearrange content inside Sections — only move the Sections themselves |
| Deprecated at the end | Always place deprecated content in the rightmost position |
| Subcomponents after main | Subcomponent section always comes after main component sections |
| Don't remove anything | Only reposition — never delete or hide elements during cleanup |
| Sort children | Within a Section, sort sibling Component Sets/Components **alphabetically by name**, with `(Def)` prefixed items always first. E.g. "(Def) Top" → "Bottom" → "Flat Icon" |
| One Overview per component | Each distinct subcomponent gets its own Overview. Don't combine unrelated subcomponents into a single Overview just because they share a Section. E.g. Primary Actions, Secondary Actions, Meta Navigation, and Brand each get their own Overview — even though they all live in the "↳ Subcomponents" Section. |
| Overview order | Overviews are ordered: Main component(s) first, then Subcomponent overviews. Within each group, order alphabetically. |
| Status suffix | Component Sets with "(Beta)", "(Deprecated)" etc. keep their suffixes |

## Example: Drawer Page Layout

```
x=0:     Component.Overview (Drawer)        [1440px wide]
x=1540:  Component.Overview (Drawer Header) [1440px wide]  
x=3080:  Component.Overview (Drawer Footer) [1440px wide]
x=4620:  Drawer → (Def) Right              [Component Set]
x=5800:  Drawer → Left                     [Component Set]
x=6980:  Drawer → Bottom                   [Component Set]
x=9285:  Drawer → Top                      [Component Set]
x=11590: Section "↳ Subcomponents"          [Header + Footer components]
x=12170: Section "❌ deprecated"            [Old deprecated sets]
```

## Notes

- Run this skill **after** creating/updating Component Overviews
- Large pages with many variants may need manual judgment for grouping
- If a component has both Desktop and Mobile variants as separate sets, group them in their own Sections
- The `db-ux-component-overview` skill references this skill for final page organization
