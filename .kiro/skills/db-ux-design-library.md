---
inclusion: auto
---

# DB UX Design System – Figma Library Conventions (Core Components)

> **Status:** 🧪 Concept — used by DB UX Design Team, iterating on rules. Not yet packaged as a Power.

This skill describes the naming patterns, property conventions, and structural rules for maintaining and extending the DB UX Design System Core Components Figma Library (v3). It works hand-in-hand with the DB UX MCP server.

## Related Skills

| Task | Skill |
|------|-------|
| Create/fill Component.Overview documentation frames | `db-ux-component-overview` |
| Organize page layout (position Sections, sort, spacing) | `db-ux-component-page-layout` |
| Review library/component quality (spacing, colors, naming) | `db-ux-design-review` |

**When to use which:**
- Building a new component or adding variants → start with this skill (conventions)
- Done building → run `db-ux-component-overview` to create documentation
- After overview → run `db-ux-component-page-layout` to clean up the page
- Before publishing → run `db-ux-design-review` to validate quality

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

**Description rule:** The Figma description of a subcomponent must always start with the warning line:

```
⚠️ Only use the subcomponent within the main component!
```

Any additional description text follows after this line.

**Examples:**

- `↳ Control Panel → BRAND`
- `↳ Control Panel → PRIMARY ACTION (Pre-Release)`
- `↳ Control Panel → SECONDARY ACTIONS (Pre-Release)`

### 1.3 Helper Components

Helper components that are only used internally (not published) are prefixed with a dot `.`:

```
.HelperName
```

**Description rule:** The Figma description of a helper component must always start with the warning line:

```
⚠️ Only use the helpercomponent within the main component!
```

Any additional description text follows after this line.

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

| Icon | Purpose                               | Example         |
| ---- | ------------------------------------- | --------------- |
| 👁️   | Show/Hide toggle (toggles visibility) | `👁️ Show Label` |
| 🔀   | Variant selection                     | `🔀 Variant`    |
| 📦   | Slot (content area)                   | `📦 Children`   |
| 🔄   | Icon swap (instance swap for icons)   | `🔄 Icon`       |
| ✏️   | Text override                         | `✏️ Label`      |

> **Note:** The `🎨` design-only prefix and `💻` dev-only prefix are **deprecated** and no longer used. Since Code Connect handles the mapping between design properties and code props, all properties are now named purely from a design perspective:
>
> - Variants always use `🔀` (including Interaction State)
> - Show/Hide toggles always use `👁️` (even if technically implemented as a Variant with True/False values)
> - Show/Hide properties should be placed directly above the property they control (e.g. `👁️ Show Children Slot` directly above `📦 Children`)
> - Instance swaps always use `🔄`
> - Slots always use `📦`
> - Text overrides always use `✏️`

### 2.3 Default Values

- Default values are always prefixed with `(Def)`: `(Def) Enabled`, `(Def) False`, `(Def) Medium`
- The `(Def)` value must always be listed **first** in the property's value list. Example: `(Def) Top, Bottom` – not `Bottom, (Def) Top`.
- The default variant is positioned first (top-left) in the variant matrix.

### 2.4 Code Connect Helper Components

Each component contains a hidden `.⚙️ Code Connect` instance that stores metadata for the code generation bridge.

**Size:** Always `0 × 0` px (effectively invisible).

**Placement:** Always the **first child** of the component's root frame, **absolutely positioned** (not part of the Auto Layout flow), **locked** (`locked: true`).

**Content per component:** Contains Code Connect metadata mapping Figma properties to framework component props. This enables the DB UX MCP server to resolve component usage from Figma nodes.

### 2.5 FloatingContainer

Interactive components (e.g. Button, Icon Button) include a `FloatingContainer` instance for floating UI elements (tooltips, popovers, badges).

**Placement:** Always the **second child** (index 1, directly after `.⚙️ Code Connect`), **absolutely positioned**. In exceptional cases (e.g. complex components with multiple nested containers), the FloatingContainer may also be placed inside a Frame directly below Code Connect rather than at the component root.

**Setup rules:**

- Constraints: `STRETCH` horizontal + `STRETCH` vertical (Left & Right, Top & Bottom)
- `visible: false` by default, bound to a `👁️ Show FloatingContainer` boolean property
- **Locked** (`locked: true`) — prevents accidental selection/movement by users
- Resized to match the variant's dimensions
- The `↳ FloatingItem` inside the slot should be set to `Position=top-start, Edge=center` (unless the component requires a different placement)

---

## 3. Component Structure

### 3.1 Auto Layout (Canvas Stacking)

All components use Auto Layout exclusively. Key rules:

- **Root frame**: Auto Layout with the component's primary stacking direction.
- **Nested containers**: Each semantic group (icon, label, actions) gets its own Auto Layout frame.
- **Spacing**: Always uses DB Design Tokens via variables – never hard-coded values.
- **Variable Source**: All variables (spacing, colors, typography, etc.) come from the **Core Foundation Library** (`@db-ux/core-foundations`). The Core Components Library does **not** define its own variables — it only consumes them. Exceptions (local variables in the Components Library) are rare and must be explicitly confirmed during review.
- **Canvas stacking** is set to **"First on top"** (default). This means the first child in the layer list renders on top visually. Used for overlapping elements (Focus rings, tooltips, popovers) where children need to visually overlap without affecting layout flow.
- **Resizing behavior**: Components are set to `Hug contents` by default. Full-width variants use `Fill container`.

### 3.2 Layer Hierarchy (Standard Pattern)

```
Component (Symbol/Variant)
├── .⚙️ Code Connect              ← Hidden metadata (0×0, first child)
├── FloatingContainer              ← Absolutely positioned, STRETCH constraints, hidden + locked
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

**Restricted slots (preferred instances):**

When a slot has "Only allow preferred instances" enabled (i.e. only specific components are valid), the slot's description must include a hint line:

```
💡 Only accepts **[Component Name]**
```

- The component name must be **bold**.
- The "Only accepts" line is always the **first line** in the description. If there is additional description text, it follows after.
- Use the `↳` prefix for subcomponents (e.g. `💡 Only accepts ↳**ControlPanel -> Brand**`).

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

> For detailed page layout rules (positioning, spacing, Section organization), see the `db-ux-component-page-layout` skill.

Each component page contains:

- **Component.Overview instances** — documentation frames (see `db-ux-component-overview` skill)
- **Sections** — grouping Component Sets by variant/direction/maturity
- **Component Sets** — containing the variant matrices

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
