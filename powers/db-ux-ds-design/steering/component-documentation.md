# DB UX Design System – Component Documentation (Platform)


This skill describes the structure, format, and conventions for creating and maintaining Figma documentation files that feed the DB UX Design System documentation platform (https://design-system.deutschebahn.com).

## Related Skills

| Task | Skill |
|------|-------|
| Build/extend components in the Figma library | `db-ux-design-library` |
| Create/fill Component.Overview frames | `db-ux-component-overview` |
| Organize page layout in component library | `db-ux-component-page-layout` |
| Review library/component quality | `db-ux-design-review` |

---

## Overview

Each component in the DB UX Design System has a dedicated **Figma documentation file** (separate from the component library). These files store all content that appears on the platform documentation pages — descriptions, guidelines (Do/Dont), resource links, and related components.

### Project Location

All documentation files live in a single Figma project:
- **Project:** https://www.figma.com/files/1555127211835288633/project/4915706988

### Naming Convention

Documentation files are named:
```
Doku – [Component Name]
```

**Examples:**
- `Doku – Shell`
- `Doku – Button`
- `Doku – Navigation`

---

## File Structure

Each documentation file consists of multiple **Figma pages**:

| Page Type | Naming Pattern | Purpose |
|-----------|---------------|---------|
| Content Page | `📋 Content [Component Name]` | Main content — structured text defining all documentation sections |
| Guideline Page (Do) | `Guideline: [Topic] (do)` | Visual example showing correct usage |
| Guideline Page (Dont) | `Guideline: [Topic] (dont)` | Visual example showing incorrect usage |

### Example File Layout (Shell Desktop)

```
📋 Content Shell Desktop          ← Main content page
Guideline: ControlPanel Position (do)
Guideline: ControlPanel Position (dont)
Guideline: SubNavigation Placement (do)
Guideline: SubNavigation Placement (dont)
Guideline: Navigation Depth (do)
Guideline: Navigation Depth (dont)
```

---

## Content Page Format

The Content Page contains a single **text node** (named with the full content) at page level. The text follows a strict structured format using `===` as section separators.

### Full Schema

```
COMPONENT: [Component Name]

DESCRIPTION:
[Main description paragraph]
- [Key feature / use case 1]
- [Key feature / use case 2]
- [Key feature / use case 3]

===

RESOURCES:
React: [URL or empty]
Vue: [URL or empty]
Angular: [URL or empty]
Web C.: [URL or empty]

===

GUIDELINE:
Headline: [Guideline Title]
Text: [Guideline description / rationale]
Do: [Figma URL to do-page] | [Caption for the Do example]
Dont: [Figma URL to dont-page] | [Caption for the Dont example]

===

GUIDELINE:
Headline: [Another Guideline Title]
Text: [Description]
Do: [Figma URL] | [Caption]
Dont: [Figma URL] | [Caption]

===

RELATED:
[Component Name 1] | [Short description of the related component]
[Component Name 2] | [Short description]
```

### Section Rules

| Section | Required | Notes |
|---------|----------|-------|
| COMPONENT | ✅ Yes | Exact component name as shown on the platform |
| DESCRIPTION | ✅ Yes | First line is a summary paragraph. Bullet points (`- `) list key features/use cases |
| RESOURCES | ✅ Yes | Framework links. Leave value empty if not yet available (e.g. `React:`) |
| GUIDELINE | ⚠️ At least one recommended | Each guideline is a separate `===`-delimited block. Multiple guidelines allowed |
| RELATED | Optional | List of related components with pipe-separated descriptions |

### Format Details

| Element | Format | Example |
|---------|--------|---------|
| Section separator | `===` on its own line | `===` |
| Guideline Do/Dont URL | Full Figma URL with `node-id` param | `https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=1-1132` |
| URL-Caption separator | ` \| ` (space-pipe-space) | `Do: [URL] \| Use Top position for simple apps.` |
| Bullet points | `- ` (dash + space) | `- Provide consistent page structure` |
| Resource frameworks | Fixed labels | `React:`, `Vue:`, `Angular:`, `Web C.:` |

---

## Guideline Pages

Each guideline referenced in the content has **two companion pages** — one for the "Do" example and one for the "Dont" example. Each visual gets its own dedicated page.

### Page Structure

```
Page: "Guideline: [Topic] (do)" or "Guideline: [Topic] (dont)"
└── Container (Frame, 480×270)
    └── [Image / Component Instance / Screenshot]
```

### Container Specifications

| Property | Value |
|----------|-------|
| Name | `Container` |
| Size | **480 × 270 px** (fixed) |
| Aspect Ratio | **Locked** (16:9) |
| Clip Content | **Enabled** |
| Auto Layout | ✅ Enabled |
| Direction | Vertical (or Horizontal depending on content) |
| Alignment | **Center, Center** (primary axis: center, counter axis: center) |
| Padding | **None** (0 on all sides) |
| Gap | `spacing-fixed-md` (DB UX spacing token) |
| Fill | **None** (no background) |
| Stroke | **None** |

### Rules

| Rule | Details |
|------|---------|
| Page naming | `Guideline: [Exact Headline from content] (do)` / `(dont)` |
| One visual per page | Each Do or Dont example is a separate Figma page |
| Container frame | Named `Container`, 480×270, Auto Layout, center-aligned |
| Content | A screenshot, component instance, or illustration showing the usage pattern |
| Image naming | Typically `image [N]` (auto-generated from fills) |
| Image format | Usually a rectangle with an image fill (exported screenshot) |
| Referencing | The Content Page text references these pages via their Figma node URL |

### URL Format for References

The Figma URL in the content text references the **page node ID**:
```
https://www.figma.com/design/[FILE_KEY]/[FILE_NAME]?node-id=[PAGE_NODE_ID]
```

Where `PAGE_NODE_ID` is the canvas/page ID (e.g. `1-1132`, `3-1110`).

---

## Creating New Documentation

### Workflow

1. **Create a new Figma file** in the documentation project, named `Doku – [Component]`
2. **Create the Content Page** named `📋 Content [Component Name]`
3. **Write the structured text** following the schema above
4. **Create Guideline pages** for each Do/Dont pair
5. **Add visual examples** to each Guideline page (screenshots or component instances)
6. **Update the content text** with the correct node-id URLs pointing to the Guideline pages

### Adding a New Guideline to an Existing File

1. Create two new pages: `Guideline: [Topic] (do)` and `Guideline: [Topic] (dont)`
2. Add visual containers with example content to each page
3. Get the node-IDs of the new pages
4. Add a new `=== GUIDELINE:` block to the Content Page text with the correct URLs

### Content Writing Guidelines

| Aspect | Guidance |
|--------|----------|
| Description | Start with a clear one-sentence summary. Follow with 2–4 bullet points highlighting key features or use cases. |
| Guideline Headline | Short, descriptive title (e.g. "ControlPanel Position", "Navigation Depth") |
| Guideline Text | Explain the rationale / when to apply this pattern. Keep concise (1–2 sentences). |
| Do Caption | Describe what the correct example shows — phrased as instruction ("Use X for Y") |
| Dont Caption | Describe what's wrong — phrased as prohibition ("Do not use X when Y") |
| Related entries | List components that are commonly used together or are variants of the documented component |

---

## Multi-Component Files

Some components share a file but have separate Content Pages for different variants or contexts:

```
Doku – Shell
├── 📋 Content Shell Desktop
├── 📋 Content Shell Mobile
├── 📋 Content Shell SubNavigation
├── 📋 Content Shell Content
├── Guideline: ControlPanel Position (do)
├── Guideline: ControlPanel Position (dont)
└── ...
```

Each `📋 Content [Variant]` page is a self-contained documentation entry with its own COMPONENT, DESCRIPTION, RESOURCES, GUIDELINE, and RELATED sections.

### Shared Guidelines

Guideline pages can be referenced by multiple Content Pages within the same file. The same Do/Dont page URL can appear in multiple GUIDELINE blocks across different Content Pages.

---

## Platform Integration

The structured text format is parsed by the documentation platform build system. Key constraints:

- **Section keywords are case-sensitive** — always use `COMPONENT:`, `DESCRIPTION:`, `RESOURCES:`, `GUIDELINE:`, `RELATED:`
- **Separator is exactly `===`** — three equals signs on a dedicated line
- **Line breaks matter** — each field (Headline, Text, Do, Dont) must be on its own line
- **URLs must be valid Figma URLs** — the platform resolves them to extract the visual content
- **Pipe separator** in Do/Dont and RELATED is ` | ` (space-pipe-space)

---

## Example: Complete Content Page Text

```
COMPONENT: Shell Desktop

DESCRIPTION:
The Shell Desktop component provides the outermost layout structure for desktop applications. It combines the ControlPanel with optional SubNavigation and a content area.
- Provide consistent page structure for desktop web applications
- Combine navigation, branding, and content areas in a single layout component
- Support different ControlPanel positions (Top, Left) and SubNavigation configurations

===

RESOURCES:
React:
Vue:
Angular:
Web C.:

===

GUIDELINE:
Headline: ControlPanel Position
Text: Choose the ControlPanel position based on navigation complexity. Top works for simple apps; Left suits applications with many navigation items or deep structures.
Do: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=1-1132 | Use the Top position for applications with few top-level navigation entries.
Dont: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=1-1133 | Do not use the Left position when only 2–3 navigation items exist. The vertical panel takes up space without benefit.

===

GUIDELINE:
Headline: SubNavigation Placement
Text: Combine the ControlPanel and SubNavigation positions to support your information architecture. ControlPanel Top + SubNavigation Left works well for complex B2B applications.
Do: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=3-1110 | Use SubNavigation Left for pages with many sub-level entries that benefit from a persistent vertical overview.
Dont: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=3-1112 | Do not switch SubNavigation position when navigating between top-level sections. Users expect the navigation layout to stay stable.

===

GUIDELINE:
Headline: Navigation Depth
Text: Multi-level navigation (Popover with nested levels) helps users find deeply nested content. Apply multi-level in either the ControlPanel or the SubNavigation – never in both.
Do: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=3-1113 | Use multi-level navigation in one area only, and keep the other flat.
Dont: https://www.figma.com/design/RcpbbqfJwjNRlOwPhAX1im/Doku--Shell?node-id=3-1114 | Do not use multi-level Popovers in both ControlPanel and SubNavigation at the same time. This makes orientation harder for users.

===

RELATED:
Shell Mobile | Mobile variant of the Shell with responsive navigation patterns.
Shell SubNavigation | Secondary navigation within the Shell for sub-level page structure.
Shell Content | The scrollable content area of the Shell with slot-based layout.
```

---

## Notes

- Documentation files are **separate from the component library** — they live in their own project folder
- The platform reads these files automatically — changes in Figma propagate to the live documentation
- Guideline images should be **clear and minimal** — show only what's needed to illustrate the point
- Container sizes for guidelines can vary but should use consistent dimensions within a file
- When referencing guideline pages, always use the **page-level node-id** (canvas node), not a frame inside it
- Resource URLs point to the framework-specific documentation pages on the platform (leave empty if not yet available)
