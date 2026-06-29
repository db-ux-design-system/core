---
inclusion: manual
---

# DB UX Design System – Figma Library & Component Review

> **Status:** 🧪 Concept — used by DB UX Design Team, iterating on rules. Not yet packaged as a Power.

This skill provides a structured checklist for reviewing the DB UX Design System Figma libraries at two levels:

1. **Library Level** — File structure, page organization, naming conventions
2. **Component Level** — Internal component quality (spacing, colors, naming, structure)

It is used to verify that the library follows the conventions defined in `db-ux-design-library.md`.

## How to Use

When the user asks for a "Design Review", run the checks below against the specified scope:
- **Library Review** — checks Section A (library structure)
- **Component Review** — checks Section B (component internals) for specified pages/components
- **Full Review** — both

Report findings grouped by category, sorted by severity. Do not fix anything unless explicitly asked.

**Important:** Always re-read the current state from Figma before reporting. Do not rely on cached data from earlier in the conversation.

---

## A. Library Structure

### A1. Page Organization

| Check | Rule |
|-------|------|
| Component pages | Each component has its own dedicated page |
| Page naming | Page name matches the component name (including maturity suffix if applicable) |
| Maturity suffix | Pages for non-stable components include `(Beta)` or `(Pre-Release)` suffix |
| Deprecated marking | Deprecated pages/components are prefixed with `❌` |
| Page order | Pages are organized logically (alphabetical or by category) |
| Separator pages | `---` pages exist as visual dividers between logical sections |

### A2. Required Pages

| Check | Rule |
|-------|------|
| Cover page | `🖼 Cover` page exists as first page |
| Welcome page | `👋 Welcome & Getting Started` page exists |
| Overview page | `🪜 Overview` page exists |
| Changelog | `📋 Changelog` page exists |
| Helper page | `🛟 Helper` page exists (for shared helper components) |
| Code Connect page | `⚙️ Code Connect` page exists |

### A3. Page Content Structure

| Check | Rule |
|-------|------|
| Sections | Component page uses Figma Sections to group variants (by size, type, etc.) |
| Section naming | Sections use descriptive names: size variants `(Def) Medium`, `Small`, or type groupings |
| Subcomponents section | If subcomponents exist, they are grouped in a `↳ Subcomponents` section |
| Variant frames | Variant matrices are contained in named frames following `Component → Variant` pattern |

### A4. Component Publication

| Check | Rule |
|-------|------|
| Published components | Main components and subcomponents are published (not hidden from library) |
| Helper components hidden | Helper components (`.` prefix) are NOT published to the library |
| Descriptions | Published components have a description with at minimum a documentation link |
| Documentation link | Description contains a link to the design system docs site |

---

## B. Component Internals

### B1. Spacing & Tokens

Scan all FRAME, SLOT, and COMPONENT nodes (skip INSTANCE children).

| Check | Rule |
|-------|------|
| Untokenized gap | `itemSpacing > 0` without `boundVariables.itemSpacing` → should be 0 or token-bound |
| Untokenized padding | `paddingTop/Bottom/Left/Right > 0` without corresponding `boundVariables` → should be 0 or token-bound |
| Untokenized corner radius | `topLeftRadius > 0` (etc.) without `boundVariables` → should be token-bound |
| Untokenized stroke width | `strokeTopWeight > 0` without `boundVariables` → should be token-bound |
| Local variables | Variables bound to nodes should come from the **Core Foundation Library**, not be locally defined. Flag any local variable usage — these are exceptions that must be explicitly confirmed. |

### B2. Colors & Fills

| Check | Rule |
|-------|------|
| Hardcoded fill color | Solid fill without `fill.boundVariables.color` (ignore white `rgb(255,255,255)` and fully transparent) |
| Hardcoded stroke color | Solid stroke without `stroke.boundVariables.color` |
| Hardcoded text color | Text node fills without `boundVariables.color` (ignore black `rgb(0,0,0)` as default) |

### B3. Naming

| Check | Rule |
|-------|------|
| Generic names | Matches `Frame \d+`, `Rectangle \d+`, `Group \d+`, `Vector`, `Line \d+`, `Image \d+` |
| Copy suffixes | Name ends with ` copy`, ` Copy`, or trailing number suggesting duplication |
| Former slots | Node has `📦` in name but `type !== "SLOT"` → emoji should be removed |
| Duplicate Container | A node named "Container" directly contains another "Container" child |
| Lowercase frame names | FRAME/SLOT starting with lowercase (exceptions: prefixed with `↳` or emoji) |
| Very short names | FRAME/SLOT with name ≤ 2 characters |

### B4. Slot Integrity

| Check | Rule |
|-------|------|
| Slot sizing | SLOT nodes should have `layoutSizingVertical: "FILL"` and `layoutSizingHorizontal: "HUG"` (unless parent layout direction requires otherwise). **Note:** Hidden/collapsed slots may appear as FIXED height — this is a Figma display artifact. Only flag slots that are actually visible and still FIXED. |
| Slot visibility props | Hidden slots should have a corresponding `👁️ Show ...` boolean property |

### B5. Code Connect

> **Note:** Code Connect is not yet integrated in all components. Skip the "placement" and "first child" check for components where CC is intentionally missing. However, if CC exists, always fix its size to exactly 0×0.

| Check | Rule | Auto-fix |
|-------|------|----------|
| Placement | `.⚙️ Code Connect` must be the **first child** of each component variant | — |
| Size | Must be exactly `0×0` px | ✅ Always resize to 0×0 if any deviation is found |
| Position | Must be absolutely positioned (not in Auto Layout flow) | — |
| Locked | Must have `locked: true` | — |

### B6. FloatingContainer

| Check | Rule |
|-------|------|
| Placement | Must be the **second child** (index 1, after Code Connect) or inside a frame directly below CC |
| Constraints | `STRETCH` horizontal + `STRETCH` vertical |
| Visibility | `visible: false` by default |
| Locked | `locked: true` |
| Size | Should match variant dimensions |

### B7. Icon Pattern

| Check | Rule |
|-------|------|
| Structure | Icons must follow: `Icon Container > Icon Color + Icon Component > <Icon Instance>` |
| Icon Color | Must be a shape node (RECTANGLE or similar) carrying a fill color variable |
| Icon Component | Must be a FRAME containing an icon instance |

### B8. Layer Structure

| Check | Rule |
|-------|------|
| Empty visible frames | FRAME/SLOT with `children.length === 0` and `visible !== false` (note: empty slots as placeholders are OK) |
| Consistent naming across variants | Same structural layers in a component set should have identical names in every variant |
| Layer order | Within each variant of a set, structural layers should appear in the same order |

### B9. Properties

> Property conventions defined in `db-ux-design-library` § 2. Checks validate compliance:

| Check | Rule |
|-------|------|
| Property emoji prefix | Variants use `🔀`, toggles use `👁️`, slots use `📦`, icon swaps use `🔄`, text overrides use `✏️` (see `db-ux-design-library` § 2.2) |
| Default value format | Default values start with `(Def)` |
| Deprecated prefixes | Properties should NOT use `🎨` or `💻` (deprecated) |

### B10. Accessibility

| Check | Rule |
|-------|------|
| Screenreader text size | `Text Screenreader` / `Label Screenreader` nodes must be `0×0` px |
| Screenreader position | Must be absolutely positioned (not in Auto Layout flow) |

### B11. Component Overview Completeness

| Check | Rule |
|-------|------|
| Overview exists | Each component page should have a `Component.Overview` instance |
| All properties documented | Every visible `🔀` and `👁️` property from the component set is represented in the Properties section |
| All slots documented | Every `📦` slot is visualized in the Slots section |
| ⚙️ CC properties listed | Exposed Code Connect properties (visible on first level, settable by designer) are documented |
| Interaction States shown | If the component has `🔀 Interaction States`, all values are shown |
| State badge set | State badge matches the component's maturity (Stable/Beta/Pre-Release) |
| Documentation link set | Link 1 points to the correct docs URL |

---

## Output Format

Report results as:

```
## Design Review: [Component/Page Name]

### ✅ Passed
- [List of categories with no issues]

### ⚠️ Findings

#### [Category Name] (X issues)
| Component | Layer | Issue |
|-----------|-------|-------|
| ... | ... | ... |
```

Group by category, sort categories by number of issues (most first). Deduplicate repetitive findings (e.g. "📦 Start Slot in all 9 variants" instead of listing 9 rows).
