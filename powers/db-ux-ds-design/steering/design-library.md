# DB UX Design System – Figma Library-Konventionen (Core Components)


Dieses Steering beschreibt Naming-Pattern, Property-Konventionen und Strukturregeln für die Pflege und Erweiterung der DB UX Design System Core Components Figma-Library (v3). Es arbeitet Hand in Hand mit dem DB UX MCP Server.

## Verwandte Steerings

| Aufgabe | Steering |
|---------|----------|
| Component.Overview-Dokumentationsframes erstellen/ausfüllen | `db-ux-component-overview` |
| Seitenlayout organisieren (Sections positionieren, sortieren, Abstände) | `db-ux-component-page-layout` |
| Library-/Komponentenqualität prüfen (Abstände, Farben, Naming) | `db-ux-design-review` |

**Wann welches Steering verwenden:**
- Neue Komponente bauen oder Varianten hinzufügen → mit diesem Steering starten (Konventionen)
- Fertig gebaut → `db-ux-component-overview` für die Dokumentation ausführen
- Nach der Overview → `db-ux-component-page-layout` zum Aufräumen der Seite
- Vor dem Publish → `db-ux-design-review` zur Qualitätsprüfung

## Quell-Library

- **Core Components:** [DB-UX-DS-v3 — Core Components](https://www.figma.com/design/mlJ6R0GkfR15a93KSlqXtB)
  Enthält stabile, produktionsreife Basiskomponenten (Button, Checkbox, Input etc.) mit vollständigen Variantenmatrizen.

---

## 1. Naming

### 1.1 Komponenten

Hauptkomponenten folgen diesem Muster:

```
COMPONENT NAME → Variant - SubVariant
```

- **Pfeil `→`** trennt den Komponentennamen von der ersten Variant-Dimension.
- **Bindestrich `-`** trennt weitere Sub-Variant-Dimensionen.
- **`(Def)`** kennzeichnet den Standardwert einer Variant-Property.

**Beispiele:**

- `Checkbox → (Def) Medium - (Def) Auto Width`
- `Checkbox → Small - Full Width`
- `Button → (Def) Filled`
- `Icon Button → Ghost`
- `Notification → Standalone - (Def) Icon`

### 1.2 Subkomponenten

Subkomponenten werden mit `↳` vorangestellt und verwenden GROSSBUCHSTABEN für den Rollennamen:

```
↳ Parent → SUBCOMPONENT ROLE (Maturity)
```

**Beschreibungsregel:** Die Figma-Beschreibung einer Subkomponente muss immer mit folgender Warnzeile beginnen:

```
⚠️ Only use the subcomponent within the main component!
```

Zusätzlicher Beschreibungstext folgt danach.

**Beispiele:**

- `↳ Control Panel → BRAND`
- `↳ Control Panel → PRIMARY ACTION (Pre-Release)`
- `↳ Control Panel → SECONDARY ACTIONS (Pre-Release)`

### 1.3 Hilfskomponenten

Hilfskomponenten, die nur intern verwendet werden (nicht publiziert), erhalten einen Punkt `.` als Präfix:

```
.HelperName
```

**Beschreibungsregel:** Die Figma-Beschreibung einer Hilfskomponente muss immer mit folgender Warnzeile beginnen:

```
⚠️ Only use the helpercomponent within the main component!
```

Zusätzlicher Beschreibungstext folgt danach.

**Beispiele:**

- `.⚙️ Code Connect`
- `.Slot – Single`

### 1.4 Layer-Benennung

Layer innerhalb einer Komponente folgen einer semantischen Benennung:

| Layer-Typ | Benennungsmuster | Beispiel |
|-----------|------------------|----------|
| Haupt-Wrapper | `Container` | `Container` |
| Semantische Gruppe | `[Funktion] Container` | `Label Container`, `Icon Container`, `Action Section` |
| Textinhalt | Beschreibender Name | `Label`, `Text`, `Headline` |
| Icon-Farbträger | `Icon Color` | `Icon Color` |
| Icon-Instanz-Wrapper | `Icon Component` | `Icon Component` |
| Versteckter A11y-Text | `Text Screenreader` oder `Label Screenreader` | `Text Screenreader` |
| Slot-Platzhalter | `Children` oder `Slot` | `Children` |
| Pflichtfeld-Indikator | `Required Container` | `Required Container` |

---

## 2. Properties

### 2.1 Property-Benennung

Properties verwenden **Title Case** mit Leerzeichen:

- `Interaction State`
- `Show Sub Navigation`
- `Control Panel Desktop Position`

Boolean-Properties beginnen typischerweise mit einem Verb oder Adjektiv:

- `Disabled`, `Checked`, `Indeterminate`, `Expanded`
- `Show Label`, `Show Icon`

### 2.2 Property-Icons

Properties erhalten Emoji-Präfixe, die ihren Typ und Zweck kennzeichnen:

| Icon | Zweck | Beispiel |
|------|-------|----------|
| 👁️ | Ein-/Ausblenden (Sichtbarkeits-Toggle) | `👁️ Show Label` |
| 🔀 | Variantenauswahl | `🔀 Variant` |
| 📦 | Slot (Inhaltsbereich) | `📦 Children` |
| 🔄 | Icon-Tausch (Instance Swap für Icons) | `🔄 Icon` |
| ✏️ | Text-Override | `✏️ Label` |

> **Hinweis:** Die Präfixe `🎨` (design-only) und `💻` (dev-only) sind **veraltet** und werden nicht mehr verwendet. Da Code Connect das Mapping zwischen Design-Properties und Code-Props übernimmt, werden alle Properties nun rein aus Design-Perspektive benannt:
>
> - Varianten verwenden immer `🔀` (einschließlich Interaction State)
> - Ein-/Ausblende-Toggles verwenden immer `👁️` (auch wenn technisch als Variant mit True/False-Werten implementiert)
> - Show/Hide-Properties sollten direkt über der Property platziert werden, die sie steuern (z. B. `👁️ Show Children Slot` direkt über `📦 Children`)
> - Instance Swaps verwenden immer `🔄`
> - Slots verwenden immer `📦`
> - Text-Overrides verwenden immer `✏️`

### 2.3 Standardwerte

- Standardwerte erhalten immer das Präfix `(Def)`: `(Def) Enabled`, `(Def) False`, `(Def) Medium`
- Der `(Def)`-Wert muss immer **an erster Stelle** in der Werteliste stehen. Beispiel: `(Def) Top, Bottom` – nicht `Bottom, (Def) Top`.
- Die Default-Variante wird als erste (oben links) in der Variantenmatrix positioniert.

### 2.4 Code Connect Hilfskomponenten

Jede Komponente enthält eine versteckte `.⚙️ Code Connect`-Instanz, die Metadaten für die Code-Generierungs-Brücke speichert.

**Größe:** Immer `0 × 0` px (effektiv unsichtbar).

**Platzierung:** Immer das **erste Kind** des Root-Frames der Komponente, **absolut positioniert** (nicht Teil des Auto-Layout-Flows), **gesperrt** (`locked: true`).

**Inhalt pro Komponente:** Enthält Code Connect Metadaten, die Figma-Properties auf Framework-Component-Props mappen. Das ermöglicht dem DB UX MCP Server, die Komponentenverwendung aus Figma-Nodes aufzulösen.

### 2.5 FloatingContainer

Interaktive Komponenten (z. B. Button, Icon Button) enthalten eine `FloatingContainer`-Instanz für schwebende UI-Elemente (Tooltips, Popovers, Badges).

**Platzierung:** Immer das **zweite Kind** (Index 1, direkt nach `.⚙️ Code Connect`), **absolut positioniert**. In Ausnahmefällen (z. B. komplexe Komponenten mit mehreren verschachtelten Containern) kann der FloatingContainer auch innerhalb eines Frames direkt unterhalb von Code Connect platziert werden statt auf Komponentenebene.

**Setup-Regeln:**

- Constraints: `STRETCH` horizontal + `STRETCH` vertikal (Left & Right, Top & Bottom)
- `visible: false` standardmäßig, gebunden an eine `👁️ Show FloatingContainer`-Boolean-Property
- **Gesperrt** (`locked: true`) — verhindert versehentliches Auswählen/Verschieben durch User
- Auf die Abmessungen der Variante angepasst
- Das `↳ FloatingItem` innerhalb des Slots sollte auf `Position=top-start, Edge=center` gesetzt sein (es sei denn, die Komponente erfordert eine andere Platzierung)

---

## 3. Komponentenstruktur

### 3.1 Auto Layout (Canvas Stacking)

Alle Komponenten verwenden ausschließlich Auto Layout. Wichtige Regeln:

- **Root-Frame**: Auto Layout mit der primären Stapelrichtung der Komponente.
- **Verschachtelte Container**: Jede semantische Gruppe (Icon, Label, Actions) erhält einen eigenen Auto-Layout-Frame.
- **Abstände**: Verwenden immer DB Design Tokens via Variablen – niemals hart codierte Werte.
- **Variablenquelle**: Alle Variablen (Abstände, Farben, Typografie etc.) stammen aus der **Core Foundation Library** (`@db-ux/core-foundations`). Die Core Components Library definiert **keine eigenen Variablen** — sie konsumiert sie nur. Ausnahmen (lokale Variablen in der Components Library) sind selten und müssen im Review explizit bestätigt werden.
- **Canvas Stacking** ist auf **"First on top"** (Standard) eingestellt. Das erste Kind in der Layer-Liste wird visuell oben gerendert. Wird für überlappende Elemente (Focus Rings, Tooltips, Popovers) verwendet, bei denen Kinder visuell überlappen müssen, ohne den Layout-Flow zu beeinflussen.
- **Resize-Verhalten**: Komponenten sind standardmäßig auf `Hug contents` gestellt. Full-Width-Varianten verwenden `Fill container`.

### 3.2 Layer-Hierarchie (Standardmuster)

```
Component (Symbol/Variant)
├── .⚙️ Code Connect              ← Versteckte Metadaten (0×0, erstes Kind)
├── FloatingContainer              ← Absolut positioniert, STRETCH Constraints, hidden + locked
├── Container                      ← Haupt-Auto-Layout-Frame
│   ├── [Element] Container        ← Semantischer Container (z. B. "Checkbox Container")
│   │   └── Icon Container         ← Falls Element ein Icon hat
│   │       ├── Icon Color         ← Rounded Rectangle (Farbträger)
│   │       └── Icon Component     ← Frame mit der Icon-Instanz
│   ├── Label Container
│   │   ├── Label                  ← Text-Layer
│   │   └── Required Container     ← Optional, standardmäßig ausgeblendet
│   ├── Children (Slot)            ← Versteckter Slot für zusätzliche Inhalte
│   ├── Label Screenreader         ← Versteckter Text (0×0) für A11y
│   └── Required                   ← Versteckter Text (0×0) für A11y
└── Infotext                       ← Sub-Instanz für Validierungsmeldungen (optional)
```

### 3.3 Screenreader-Texte

- Versteckte Text-Layer mit Größe `0×0` px liefern Accessibility-Informationen.
- Benannt als `Text Screenreader` oder `Label Screenreader`.
- Immer **absolut positioniert** innerhalb der Komponente (nicht Teil des Auto-Layout-Flows).
- Dienen dazu, Status- oder Label-Overrides an assistive Technologien in der Code-Ausgabe zu kommunizieren.

### 3.4 Icon-Muster

Icons folgen immer dieser verschachtelten Struktur:

```
Icon Container (Frame)
├── Icon Color (Rounded Rectangle – trägt die Füllfarbe)
└── Icon Component (Frame)
    └── <IconName> (Instanz der Icon-Komponente)
```

Dieses Muster ermöglicht:

- Farbsteuerung über das `Icon Color`-Rectangle (mittels Color-Variablen).
- Icon-Tausch über die Instanz innerhalb von `Icon Component`.

### 3.5 Slots

Slots sind Frames, die als Einfügepunkte für zusätzliche Inhalte im Code dienen.

**Aktuelle Konvention** (kann sich ändern – siehe [Slots-Dokumentation](https://github.com/db-ux-design-system/core-team/blob/main/docs/content/components/Slots.md)):

- Slots verwenden Figmas natives Slot-Feature (Component-Property-Typ).
- Slots sind **nicht immer ausgeblendet**. Wenn sie standardmäßig ausgeblendet sind, gibt es eine zugehörige `👁️ Show ...`-Boolean-Property zum Togglen der Sichtbarkeit.
- Die Legacy-`.Slot`-Hilfskomponente (mit `.`-Präfix) stammt aus der Zeit vor Figmas nativen Slots und wird **schrittweise abgelöst**.

**Eingeschränkte Slots (Preferred Instances):**

Wenn bei einem Slot "Only allow preferred instances" aktiviert ist (d. h. nur bestimmte Komponenten sind gültig), muss die Slot-Beschreibung folgenden Hinweis enthalten:

```
💡 Only accepts **[Component Name]**
```

- Der Komponentenname muss **fett** sein.
- Die "Only accepts"-Zeile steht immer **an erster Stelle** in der Beschreibung. Zusätzlicher Text folgt danach.
- Bei Subkomponenten das `↳`-Präfix verwenden (z. B. `💡 Only accepts ↳**ControlPanel -> Brand**`).

**Slot-Benennungsrichtung (kommend):**

- `Children` wird primär in **Container-Komponenten** (z. B. Card, Shell) oder **neuen Komponenten** verwendet, wo der Slot den Haupt-Inhaltsbereich darstellt.
- Bei bestehenden Komponenten, wo ein Slot neben bereits existierendem Text- oder Label-Inhalt platziert wird (ergänzender/Addon-Slot), wird der Name zu `End` (oder `Start`) statt `Children`.
- Das verdeutlicht die Rolle des Slots: `Children` = primärer Inhaltsbereich, `End`/`Start` = ergänzende Addon-Position.

**Grundlegendes Slot-Konzept:**

```
┌─────────────────────────────────┐
│  Start Slot                     │
│  Children (Standardinhalt)      │
│  End Slot                       │
└─────────────────────────────────┘
```

- Slots können je nach Komponente **vertikal oder horizontal** angeordnet sein.
- `Children` ist der Standard-/Haupt-Inhalts-Slot. In manchen Komponenten zeigt das Design nur ein einfaches Text-Label statt eines vollständigen Slots — in dem Fall werden ergänzende Positionen wie `End` als zusätzliche Slots verwendet.
- `Start` und `End` sind positionelle Slots für Inhalte vor bzw. nach dem Haupt-Inhaltsbereich.

---

## 4. Variantenmatrix (Stabile Komponenten)

### Struktur

Stabile Komponenten zeigen eine vollständige Matrix aller Zustandskombinationen:

```
X-Achse: Interaction State → (Def) Enabled | Hovered | Pressed | Focused | Disabled
Y-Achse: Component State × Validation → z. B. Unchecked | Checked | Indeterminate × No Validation | Valid | Invalid
```

### Regeln

- Alle Kombinationen müssen abgedeckt sein — keine Lücken in der Matrix.
- `Disabled` wird als separate Boolean-Property (`Disabled=True`) in Kombination mit `Interaction State=None` kodiert.
- Validierungsvarianten (`Valid`, `Invalid`) enthalten immer eine Infotext-Sub-Instanz.
- Sections organisieren Varianten nach Größe (z. B. `(Def) Medium`, `Small`).

---

## 5. Komponentenseiten-Struktur

> Detaillierte Regeln zum Seitenlayout (Positionierung, Abstände, Section-Organisation) finden sich im `db-ux-component-page-layout`-Steering.

Jede Komponentenseite enthält:

- **Component.Overview-Instanzen** — Dokumentationsframes (siehe `db-ux-component-overview`-Steering)
- **Sections** — Gruppierung von Component Sets nach Variante/Richtung/Maturity
- **Component Sets** — enthalten die Variantenmatrizen

### Varianten-Frame-Layout

Innerhalb jedes Varianten-Frames sind Symbole (Komponenteninstanzen) in einem Grid angeordnet:

- Spalten = Interaction States
- Zeilen = Zustandskombinationen (Checked × Validation)
- Jedes Symbol ist mit seinem vollständigen Property-Set benannt zur einfachen Identifikation.

---

## 6. Maturity-Status

| Suffix | Bedeutung |
|--------|-----------|
| `(Pre-Release)` | Nutzbar, kann sich aber noch deutlich ändern |
| `(Beta)` | Weitgehend stabil, kleinere Änderungen möglich |
| _(keines)_ | Stabil / Produktionsreif |

Die Maturity wird im Layer-Tree an den Komponentennamen angehängt, ist aber nicht Teil der Variant-Property.

---

## 7. Neue Komponenten erstellen (Core Lab)

Beim Erstellen einer neuen Komponente in der Core Lab Library **immer zuerst die Core Components Library prüfen**, ob bestehende Komponenten als Referenz dienen können. Dabei achten auf:

- **Struktur** – Layer-Hierarchie, Container-Verschachtelung, Auto-Layout-Setup
- **Properties** – Welche Properties existieren, wie sie benannt sind, welche Icons verwendet werden
- **Naming** – Naming-Pattern für Komponenten, Varianten und Layer
- **Muster** – Icon-Pattern, Slot-Nutzung, Screenreader-Texte, Code Connect Platzierung

Den DB UX MCP Server (`mcp_db_ux_get_component_props`, `mcp_db_ux_get_component_details`) verwenden, um bestehende Komponenten als Referenz zu inspizieren, bevor neue gebaut werden.

---

## Referenzen

- Core Components Library: https://www.figma.com/design/mlJ6R0GkfR15a93KSlqXtB
- DB UX MCP Server: Verfügbar über Kiro Powers für Component Details, Props, Beispiele und Design Tokens.
