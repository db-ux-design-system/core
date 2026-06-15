---
inclusion: auto
---

# DB UX Design System – Figma Library Conventions (Core Components)

This skill describes the naming patterns, property conventions, and structural rules for maintaining and extending the DB UX Design System Core Components Figma Library (v3). It works hand-in-hand with the DB UX MCP server.

## Source Library

- **Core Components:** [DB-UX-DS-v3 — Core Components](https://www.figma.com/design/mlJ6R0GkfR15a93KSlqXtB)
  Contains stable, production-ready base components (Button, Checkbox, Input, etc.) with complete variant matrices.

---

## 1. Naming

### 1.1 Components

Main components follow this pattern:

```
COMPONENT NAME → Variant - SubVariant
```

- **Arrow `→`** separates the component name from its first variant dimension.
- **Dash `-`** separates additional sub-variant dimensions.
- **`(Def)`** marks the default value of a variant property.

**Examples:**

- `Checkbox → (Def) Medium - (Def) Auto Width`
- `Checkbox → Small - Full Width`
- `Button → (Def) Filled`
- `Icon Button → Ghost`
- `Notification → Standalone - (Def) Icon`

### 1.2 Subcomponents

Subcomponents are prefixed with `↳` and use UPPERCASE for the subcomponent's role name:

```
↳ Parent → SUBCOMPONENT ROLE (Maturity)
```

**Examples:**

- `↳ Control Panel → BRAND`
- `↳ Control Panel → PRIMARY ACTION (Pre-Release)`
- `↳ Control Panel → SECONDARY ACTIONS (Pre-Release)`

### 1.3 Helper Components

Helper components that are only used internally (not published) are prefixed with a dot `.`:

```
.HelperName
```

**Examples:**

- `.⚙️ Code Connect`
- `.Slot – Single`

### 1.4 Layer Naming

Layers within a component follow semantic naming:

| Layer Type            | Naming Pattern                              | Example                                               |
| --------------------- | ------------------------------------------- | ----------------------------------------------------- |
| Main wrapper          | `Container`                                 | `Container`                                           |
| Semantic group        | `[Function] Container`                      | `Label Container`, `Icon Container`, `Action Section` |
| Text content          | Descriptive name                            | `Label`, `Text`, `Headline`                           |
| Icon color carrier    | `Icon Color`                                | `Icon Color`                                          |
| Icon instance wrapper | `Icon Component`                            | `Icon Component`                                      |
| Hidden a11y text      | `Text Screenreader` or `Label Screenreader` | `Text Screenreader`                                   |
| Slot placeholder      | `Children` or `Slot`                        | `Children`                                            |
| Required indicator    | `Required Container`                        | `Required Container`                                  |

---

## 2. Properties

### 2.1 Property Naming

Properties use **Title Case** with spaces:

- `Interaction State`
- `Show Sub Navigation`
- `Control Panel Desktop Position`

Boolean properties typically start with a verb or adjective:

- `Disabled`, `Checked`, `Indeterminate`, `Expanded`
- `Show Label`, `Show Icon`

### 2.2 Property Icons

Properties are prefixed with emoji icons to indicate their type and purpose:

| Icon | Purpose                                | Example                |
| ---- | -------------------------------------- | ---------------------- |
| 👁️   | Show/Hide boolean (toggles visibility) | `👁️ Show Label`        |
| 🔀   | Variant selection                      | `🔀 Variant`           |
| 📦   | Slot (content area)                    | `📦 Children`          |
| 🔄   | Icon swap (instance swap for icons)    | `🔄 Icon`              |
| ✏️   | Text override                          | `✏️ Label`             |
| 🎨   | Design-only property (not in code)     | `🎨 Interaction State` |

> **Note:** The `💻` dev-only property prefix is **deprecated** and no longer used. Properties that were previously dev-only are now either:
>
> - Visible on the component's first level (if configurable by the user), or
> - Integrated invisibly as Code Connect properties inside the `.⚙️ Code Connect` helper (if not user-configurable).
>
> See: [Code Connect documentation](https://design-system.deutschebahn.com/documentation/learn/code-connect/)

### 2.3 Default Values

- Default values are always prefixed with `(Def)`: `(Def) Enabled`, `(Def) False`, `(Def) Medium`
- The default variant is positioned first (top-left) in the variant matrix.

### 2.4 Code Connect Helper Components

Each component contains a hidden `.⚙️ Code Connect` instance that stores metadata for the code generation bridge.

**Size:** Always `0 × 0` px (effectively invisible).

**Placement:** Always the **first child** of the component's root frame, **absolutely positioned** (not part of the Auto Layout flow).

**Content per component:** Contains Code Connect metadata mapping Figma properties to framework component props. This enables the DB UX MCP server to resolve component usage from Figma nodes.

---

## 3. Component Structure

### 3.1 Auto Layout (Canvas Stacking)

All components use Auto Layout exclusively. Key rules:

- **Root frame**: Auto Layout with the component's primary stacking direction.
- **Nested containers**: Each semantic group (icon, label, actions) gets its own Auto Layout frame.
- **Spacing**: Always uses DB Design Tokens via variables – never hard-coded values.
- **Canvas stacking** is set to **"First on top"** (default). This means the first child in the layer list renders on top visually. Used for overlapping elements (Focus rings, tooltips, popovers) where children need to visually overlap without affecting layout flow.
- **Resizing behavior**: Components are set to `Hug contents` by default. Full-width variants use `Fill container`.

### 3.2 Layer Hierarchy (Standard Pattern)

```
Component (Symbol/Variant)
├── .⚙️ Code Connect              ← Hidden metadata (0×0, first child)
├── Container                      ← Main Auto Layout frame
│   ├── [Element] Container        ← Semantic container (e.g. "Checkbox Container")
│   │   └── Icon Container         ← If element has an icon
│   │       ├── Icon Color         ← Rounded Rectangle (color carrier)
│   │       └── Icon Component     ← Frame containing the icon instance
│   ├── Label Container
│   │   ├── Label                  ← Text layer
│   │   └── Required Container     ← Optional, hidden by default
│   ├── Children (Slot)            ← Hidden slot for additional content
│   ├── Label Screenreader         ← Hidden text (0×0) for a11y
│   └── Required                   ← Hidden text (0×0) for a11y
└── Infotext                       ← Sub-instance for validation messages (optional)
```

### 3.3 Screenreader Texts

- Hidden text layers with size `0×0` px provide accessibility information.
- Named `Text Screenreader` or `Label Screenreader`.
- Always **absolutely positioned** within the component (not part of Auto Layout flow).
- Used to communicate state or label overrides to assistive technology in the code output.

### 3.4 Icon Pattern

Icons always follow this nested structure:

```
Icon Container (Frame)
├── Icon Color (Rounded Rectangle – carries the fill color)
└── Icon Component (Frame)
    └── <IconName> (Instance of icon component)
```

This pattern allows:

- Color control via the `Icon Color` rectangle (using color variables).
- Icon swapping via the instance inside `Icon Component`.

### 3.5 Slots

Slots are hidden frames that serve as insertion points for additional content in code.

**Current convention** (subject to change – see [Slots documentation](https://github.com/db-ux-design-system/core-team/blob/main/docs/content/components/Slots.md)):

- Slots use Figma's native Slot feature (component property type).
- Slots are **not always hidden**. When they are hidden by default, there is a corresponding `👁️ Show ...` boolean property to toggle their visibility.
- The legacy `.Slot` helper component (prefixed with `.`) predates Figma's native slots and is being **phased out** step by step.

**Slot naming direction (upcoming):**

- `Children` will primarily be used in **container components** (e.g. Card, Shell) or **new components** where the slot represents the main content area.
- For existing components where a slot is placed alongside already existing text or label content (supplementary/addon slot), the name will shift to `End` (or `Start`) instead of `Children`.
- This clarifies the slot's role: `Children` = primary content area, `End`/`Start` = supplementary addon position.

**Basic slot concept:**

```
┌─────────────────────────────────┐
│  Start Slot                     │
│  Children (default content)     │
│  End Slot                       │
└─────────────────────────────────┘
```

- Slots can be arranged **vertically or horizontally** depending on the component.
- `Children` is the default/main content slot. In some components the design may only show a simple text label instead of a full slot – in that case, supplementary positions like `End` are used as additional slots.
- `Start` and `End` are positional slots for content placed before or after the main content area.

---

## 4. Variant Matrix (Stable Components)

### Structure

Stable components display a complete matrix of all state combinations:

```
X-axis: Interaction State → (Def) Enabled | Hovered | Pressed | Focused | Disabled
Y-axis: Component State × Validation → e.g. Unchecked | Checked | Indeterminate × No Validation | Valid | Invalid
```

### Rules

- All combinations must be covered – no gaps in the matrix.
- `Disabled` is encoded as a separate boolean property (`Disabled=True`) combined with `Interaction State=None`.
- Validation variants (`Valid`, `Invalid`) always include an Infotext sub-instance.
- Sections organize variants by size (e.g. `(Def) Medium`, `Small`).

---

## 5. Component Page Structure

Each component page contains:

### Sections

- **Size-based sections**: `(Def) Medium`, `Small` etc., each containing variant frames.
- **Variant frames**: Named `Component → Size - Width Variant`, containing the full state matrix.

### Variant Frame Layout

Within each variant frame, symbols (component instances) are arranged in a grid:

- Columns = Interaction States
- Rows = State combinations (Checked × Validation)
- Each symbol is named with its full property set for easy identification.

---

## 6. Maturity Status

| Suffix          | Meaning                               |
| --------------- | ------------------------------------- |
| `(Pre-Release)` | Usable but may change significantly   |
| `(Beta)`        | Mostly stable, minor changes possible |
| _(none)_        | Stable / Production-ready             |

Maturity is appended to the component name in the layer tree but not part of the variant property.

---

## 7. Creating New Components (Core Lab)

When creating a new component in the Core Lab library, **always check the Core Components library first** for existing components that can serve as a reference. Look at:

- **Structure** – Layer hierarchy, container nesting, Auto Layout setup
- **Properties** – Which properties exist, how they are named, which icons are used
- **Naming** – Component name pattern, variant naming, layer naming
- **Patterns** – Icon pattern, slot usage, screenreader texts, Code Connect placement

Use the DB UX MCP server (`mcp_db_ux_get_component_props`, `mcp_db_ux_get_component_details`) to inspect existing components for reference before building new ones.

---

## References

- Core Components Library: https://www.figma.com/design/mlJ6R0GkfR15a93KSlqXtB
- DB UX MCP Server: Available via Kiro Powers for component details, props, examples, and design tokens.
