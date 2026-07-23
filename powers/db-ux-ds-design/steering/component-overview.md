# DB UX Design System – Component Overview Generator


Dieses Steering hilft beim Ausfüllen der `Component.Overview`-Vorlage für eine Komponente in der DB UX Core Components Figma-Library.

## Überblick

Die `Component.Overview` ist ein standardisierter Dokumentationsframe, der auf jeder Komponentenseite in Figma platziert wird. Er basiert auf einer Vorlage aus der "DB UX DS - Visuals Internal"-Library und dokumentiert Properties, Slots, Anatomie und Anwendungsbeispiele der Komponente.

## Vorlagenquelle

- **Library:** DB UX DS - Visuals Internal
- **Komponente:** `Component.Overview`
- **Component Key (Variant=Component):** `7692e799fe3fc67bebb8b636d7eec01496dd8a9a`
- **Component Set Key:** `4533ee748c9c84c93c463a341ccb06e43ea8c094`
- **Vorlagen-Datei:** https://www.figma.com/design/ssdghRkxRSFrY2AZJgPURn/Template-Component-Overview
- **Hilfskomponenten:** `Overview.Content.Container`, `Overview.Property.Value`
- **Overview.Content.Container Key:** `e3f5cfec4677892a5bc0dd8637fea60a45c83324`
- **Overview.Property.Value Key:** `daca96f1be19e01930451819b71eb7332fa302d1`

### Programmatisches Einfügen

```js
const component = await figma.importComponentByKeyAsync("7692e799fe3fc67bebb8b636d7eec01496dd8a9a");
const instance = component.createInstance();
instance.x = 0;
instance.y = 0;
```

## Platzierungsregeln

- Die Overview wird an Position **0/0** auf der Komponentenseite platziert
- Eine Subcomponent Overview (falls nötig) wird **direkt rechts** neben der Haupt-Overview platziert
- Component Sets beginnen bei **x = rechte Kante der letzten Overview + 100**, oben ausgerichtet bei **y = 0**
- Nachdem alle Overviews platziert sind, werden bestehende Component Sets und Sections rechts daneben verschoben — dabei bleibt ihre ursprüngliche relative Anordnung und ihr Layout erhalten

## Vorlagen-Abschnitte

### Header

| Feld | Quelle | Ausfüllen |
|------|--------|-----------|
| Component Name | Text-Node `"Component Name"` | Komponentenname eintragen (z. B. "Drawer") |
| State Badge | Instanz `"State"` | Variant-Property `"State"` auf der State-Instanz setzen: "Stable", "Beta", "Concept" oder "Pre-Release". Dies ist eine Variant-Property auf der verschachtelten Instanz — die State-Instanz auswählen und im Properties-Panel die Variante ändern. |
| Link 1 | Instanz `"Link → External"` | Dokumentations-URL: `https://design-system.deutschebahn.com/documentation/components/{component-name}`. Der `{component-name}` wird vom Seitennamen abgeleitet: Kleinbuchstaben, Leerzeichen, Klammern und Status-Suffixe wie "(Beta)" entfernen. Z. B. "ControlPanel (Beta)" → `controlpanel`, "Drawer" → `drawer`. |
| Link 2 | Instanz `"Link → External"` | "Submit Feedback"-Link (kann so bleiben oder auf GitHub Issues verweisen) |
| Notification | Standardmäßig ausgeblendet | Nur anzeigen, wenn ein wichtiger Nutzungshinweis existiert |

### Preview (👁️ Preview)

Zwei Reihen, die die Komponente im Light- und Darkmode zeigen:
- **Slot Light**: Repräsentative Komponenteninstanzen im Standard-/häufigsten Zustand platzieren
- **Slot Dark**: Dieselben Instanzen im Dark-Mode-Kontext

**Automatisierung:** Die Preview kann automatisch generiert werden, indem die Default-Variante (oder je eine Instanz pro Component Set bei Multi-Set-Komponenten) in beide Slots (Light und Dark) gesetzt wird. Bei Multi-Set-Komponenten wird eine repräsentative Instanz pro Richtung/Variante angezeigt.

### Anatomy (📐 Anatomy)

Manueller Abschnitt — zeigt den visuellen Aufbau der Komponente mit beschrifteten Bestandteilen. Zweck: Entwickler im Dev Mode können alle strukturellen Elemente identifizieren und die nötigen Tokens (Abstände, Farben, Typografie) extrahieren.

**Nicht automatisierbar** — die Komplexität variiert je nach Komponente. Boolean-/Variant-Kombinationen machen eine vollständige Automatisierung unpraktikabel. Manuell auf Basis der Komponentenstruktur erstellen.

`Overview.Content.Container`-Instanzen mit Komponenteninstanzen und Annotationen verwenden.

### Properties (🛠️ Properties)

Dies ist der Hauptabschnitt für die Automatisierung. Struktur:

```
Property Container
└── Contents Properties (Slot)
    ├── Overview.Content.Container  ← einer pro Property
    │   ├── Title (Text)            ← Property-Name (z. B. "🔀 Container Size")
    │   └── Content Slot
    │       ├── Overview.Property.Value  ← einer pro Wert
    │       │   ├── Value (Text)         ← Wertname (z. B. "(Def) Small")
    │       │   └── Value Slot           ← Komponenteninstanz mit diesem Wert
    │       ├── Overview.Property.Value
    │       │   ├── Value (Text)         ← "Medium"
    │       │   └── Value Slot           ← Instanz mit Medium angewendet
    │       └── ...
    ├── Overview.Content.Container  ← nächste Property
    │   └── ...
    └── ...
```

**Ermittlung der Properties:**

1. `componentPropertyDefinitions` des Component Sets auslesen
2. Nach Typ gruppieren:
   - `VARIANT`-Properties → jede Variant-Option als eigener `Overview.Property.Value` mit einer Komponenteninstanz im entsprechenden Zustand
   - `BOOLEAN` (👁️)-Properties → True/False-Zustände als zwei `Overview.Property.Value`-Einträge
3. Interne/versteckte Properties (die mit `.⚙️` beginnen) überspringen
4. Auf **Code Connect Properties** prüfen (siehe unten)

### Code Connect Properties (⚙️)

Wenn eine Komponente eine `.⚙️ Code Connect`-Instanz enthält, deren Properties **auf Komponentenebene exponiert** sind (d. h. im Properties-Panel auf der ersten Ebene sichtbar und vom Designer einstellbar), müssen diese ebenfalls im Properties-Abschnitt dokumentiert werden.

**Identifikation von Code Connect Properties:**

1. Die `componentPropertyDefinitions` des Component Sets prüfen
2. Properties mit `⚙️`-Präfix (z. B. `⚙️ Direction`, `⚙️ Position`, `⚙️ Emphasis`), die exponiert sind (nicht innerhalb von `.⚙️` — der Punkt-Präfix bedeutet versteckt), sind Code Connect Properties
3. Es handelt sich typischerweise um VARIANT- oder BOOLEAN-Properties auf der verschachtelten `.⚙️ Code Connect`-Instanz, die über Figmas "Expose from nested instance"-Funktion auf Komponentenebene sichtbar gemacht wurden

**Darstellung:**

- Dieselbe Struktur wie bei Design-Properties: `Overview.Content.Container` → `Overview.Property.Value`
- Titel: Property-Name inklusive `⚙️`-Präfix (z. B. `⚙️ Direction`)
- Werte: Alle möglichen Werte als Text in den `Overview.Property.Value`-Einträgen auflisten
- Value Slot: **Keine Komponenteninstanz nötig** — nur den Textwert anzeigen. Code Connect Properties sind reine Code-Metadaten, eine visuelle Instanz würde keinen Unterschied zeigen. Stattdessen einen einfachen Text-Node mit dem Wertnamen verwenden.
- Alternative: Wenn die Property das Erscheinungsbild der Komponente sichtbar verändert, Instanzen wie bei regulären Variant-Properties zeigen

**Unterscheidung im Overview-Header:**

Der Properties-Abschnitt enthält ein Label im Header:
- `⚙️ code connect property` — für Code Connect Properties

So erkennen Designer, welche Properties die Code-Ausgabe (⚙️) beeinflussen.

**Beispiel (Tooltip):**

| Property | Typ | Werte |
|----------|-----|-------|
| ⚙️ Direction | Code Connect | Bottom, Top, Left, Right |
| ⚙️ Emphasis | Code Connect | Weak, Strong |
| ⚙️ Width | Code Connect | Auto, Fixed |

### Slots (🔄 Slots)

Gleiche Struktur wie Properties, aber für SLOT-Properties. Verwendet eine **visuelle Darstellung**, die die Komponente mit farblich hervorgehobenen Slots zeigt:

**Regeln für die Slot-Visualisierung:**

| Regel | Details |
|-------|---------|
| Farbe | `#EE41B9` — wird für Stroke und Fill verwendet |
| Umrandung | Alle Slots auf Komponentenebene erhalten einen **1px Stroke** in `#EE41B9` (volle Deckkraft) |
| Inaktiver Slot-Fill | `#EE41B9` mit **10% Deckkraft** — alle Slots, die NICHT der aktuell dokumentierte sind |
| Aktiver Slot-Fill | `#EE41B9` mit **30% Deckkraft** — der aktuell dokumentierte/hervorgehobene Slot |
| Versteckte Slots | Müssen sichtbar gemacht werden (`slot.visible = true`). Boolean-Properties wie `👁️ Show Start Slot` verwenden, um versteckte Slots einzublenden. |
| Verschachtelte Instanz-Slots | Müssen ausgeblendet werden (`slot.visible = false`) — nur Slots zeigen, die direkt zur Komponente gehören, nicht Slots in verschachtelten Instanzen |
| Leere Slots | **Alle Slots müssen befüllt sein** in der Slots-Visualisierung. Wenn ein Slot keinen Inhalt hat, einen "Slot Filler"-Frame (24×24px basierend auf `icon-size-md`, **kein Fill, kein Stroke, aber `visible: true`**) einfügen, damit der Slot im Layout gerendert wird. "Unsichtbar" bedeutet hier: Der Filler hat keine visuelle Erscheinung (kein Fill, kein Stroke) — er darf aber **NICHT** ausgeblendet werden (`visible: false`). Wenn der Slot bereits Kinder enthält, den Filler weglassen und nur das Farb-Styling (Fills + Strokes) anwenden. Große Slots sind typischerweise `fill`, daher reicht ein kleiner Filler; nur bei `hug`-Slots bestimmt der Filler die sichtbare Größe. Kernregel: **Jeder Slot muss mindestens ein sichtbares Kind-Element enthalten** (echten Inhalt oder einen No-Fill/No-Stroke-Filler), damit alle Slots in der Overview visuell dargestellt werden. |
| Ein PV pro Slot | Jeder `Overview.Property.Value` zeigt die Komponente mit EINEM Slot als aktiv hervorgehoben, die anderen als inaktiv |
| Value-Label immer sichtbar | Der `Value`-Text-Node in jedem `Overview.Property.Value` muss **sichtbar** bleiben und den Slot-Namen enthalten (z. B. "Children", "Start Slot"). Nur der OCC-Titel wird ausgeblendet, nicht die PV-Labels. |

**Struktur:**
```
Contents Slots (Slot)
└── Overview.Content.Container (Titel ausgeblendet)
    └── Content Slot
        ├── Overview.Property.Value (Label: "Start Slot")
        │   └── Value Slot → Komponenteninstanz mit Start Slot aktiv (pink), End Slot inaktiv (hell)
        ├── Overview.Property.Value (Label: "Children")
        │   └── Value Slot → Komponenteninstanz mit Children aktiv, andere inaktiv
        └── Overview.Property.Value (Label: "End Slot")
            └── Value Slot → Komponenteninstanz mit End Slot aktiv, andere inaktiv
```

**Implementierung:**
```js
// Für jeden Slot der Komponente:
// 1. Komponenteninstanz erstellen mit allen Slots sichtbar
// 2. Alle SLOT-Nodes finden, die NICHT in verschachtelten Instanzen liegen
// 3. Umrandung + aktive/inaktive Farben anwenden
// 4. Alle Slots innerhalb verschachtelter Instanzen ausblenden

function isInsideNestedInstance(node, rootInstance) {
  let parent = node.parent;
  while (parent && parent.id !== rootInstance.id) {
    if (parent.type === "INSTANCE") return true;
    parent = parent.parent;
  }
  return false;
}
```

**Wenn die Komponente keine Slots hat:** Den gesamten "Slots Container"-Abschnitt ausblenden.

### Examples (✨ Examples)

Optionaler Abschnitt für spezifische Nutzungsmuster. Kann zunächst leer bleiben.

### Feature Backlog (📋 Feature Backlog)

Optionaler Abschnitt mit geplanten Features. Kann leer bleiben.

### Out of Scope (❌ Out of Scope)

Optionaler Abschnitt für explizit nicht unterstützte Muster. Kann leer bleiben.

---

## Workflow

### Benötigte Eingaben

1. **Komponentenseiten-URL oder -Name** — welche Komponente dokumentiert werden soll
2. **File Key und Branch** — in welcher Figma-Datei gearbeitet wird

### Schritt 1: Vorschlag (Lesen + Analysieren)

1. `componentPropertyDefinitions` des Component Sets auslesen
2. Variant-Properties (🔀), Boolean-Properties (👁️) und Slots (📦) identifizieren
3. **Auf Multi-Set Dev Properties prüfen:** Wenn die Seite mehrere Component Sets hat, die Varianten einer logischen Komponente darstellen (z. B. "Drawer → Right", "Drawer → Left"), per `get_component_props` aus dem DB UX MCP die Code-Property (z. B. `direction`) und ihre Werte ermitteln. Diese wird als `🔀`-Variant-Property in der Haupt-Overview dokumentiert — der Designer wählt aktiv das Component Set, was es zu einer sichtbaren Design-Entscheidung macht. Intern wird der Wert über `⚙️ Code Connect` an die Entwicklung übergeben, in der Overview wird er aber als `🔀` dargestellt, weil er Designer-relevant ist.
4. Layout pro Property bestimmen:
   - **Horizontal:** ≤ 3 Werte oder Werte mit kleinen Instanzen (< 200px breit)
   - **Vertikal:** > 3 Werte oder Werte mit großen/breiten Instanzen
5. **Vorschlag dem User im Tabellenformat präsentieren:**

```
## Vorgeschlagene Overview für [Komponente]

### Header
- Name: [Komponentenname]
- Status: [Stable/Beta/Pre-Release]
- Autor: [Kürzel]
- Docs-Link: [URL]

### Properties

| Property | Layout | Werte | Instanz-Konfiguration |
|----------|--------|-------|----------------------|
| 🔀 X | Vertikal | (Def) A, B, C | variant: X=A, X=B, X=C |
| 👁️ Y | Horizontal | (Def) True, False | Default-Instanz + Y togglen |

### Slots
| Slot |
|------|
| 📦 Children |
```

5. **Auf Bestätigung oder Anpassungen des Users warten**, bevor fortgefahren wird

### Schritt 2: Ausführen (In Figma schreiben)

Erst nach Bestätigung durch den User:
1. `Component.Overview` importieren und an 0/0 platzieren
2. Header setzen (Name, State-Badge-Variante, Autor, Links)
3. Pro Property einen `Overview.Content.Container` im "Contents Properties"-Slot erstellen
4. Ausrichtung (Horizontal/Vertikal) pro Container setzen
5. Pro Wert einen `Overview.Property.Value` in jedem Content-Slot erstellen
6. Komponenteninstanzen in jedem Value Slot mit korrekter Variant-/Boolean-Konfiguration platzieren
7. Slots-Abschnitt befüllen

### Regeln beim Ausfüllen

| Regel | Details |
|-------|---------|
| Wert-Benennung | Der Standardwert erhält immer das Präfix `(Def)`: `(Def) Small`, `(Def) True`, `(Def) False` |
| Instanzgröße | **Instanzen niemals skalieren.** Bei responsiven/Layout-Komponenten, die sehr groß sind (> 700px breit oder hoch), ist `resize()` auf eine feste Größe als Ausnahme erlaubt — die Komponente passt ihr internes Layout an. Innerhalb einer Property konsistente Größen verwenden. Bei zu breiten Instanzen auf vertikales Layout wechseln. |
| Multi-Set-Property-Visualisierung | Wenn eine Property als separate Component Sets für verschiedene Richtungen/Orientierungen implementiert ist (z. B. "Drawer → Right" + "Drawer → Top"), werden **Instanzen beider Orientierungen** nebeneinander in jedem Property Value gezeigt. Das hilft Designern zu sehen, wie sich die Property über Orientierungen verhält. Horizontales Layout mit `3xl`-Gap (48px) für den Value Slot verwenden. |
| Boolean-Instanzen | Die **Default-Variante** der Komponente verwenden und nur die relevante Boolean-Property togglen |
| Variant-Instanzen | Eine dedizierte Variante aus dem Component Set verwenden, die dem Property-Wert entspricht |
| Layout-Wahl | Horizontal bei ≤ 3 Werten mit kleinen Instanzen. Vertikal bei breiten/hohen Instanzen oder > 3 Werten |
| Property-Reihenfolge | `👁️ Show X`-Boolean-Properties werden **direkt oberhalb** (= davor in der Liste) der Property platziert, die sie steuern (z. B. `👁️ Show Subnavigation` direkt über `🔀 Subnavigation Position`). Das entspricht der Konvention in `db-ux-design-library`. |
| Orientation-Property | `"Orientation": "Vertical"` oder `"Horizontal"` auf der `Overview.Content.Container`-Instanz setzen |
| Keine Properties | Wenn eine Komponente keine Variant-/Boolean-Properties hat, den "Property Container"-Abschnitt ausblenden |
| Slot-Visualisierung | Slots mit farbcodierten Komponenteninstanzen darstellen (siehe Slots-Abschnitt oben) |
| Nur Boolean/Variant | Text-Properties (✏️) und Instance-Swap-Properties (🔄) werden standardmäßig nicht in der Overview gezeigt. Instance Swaps sind typischerweise Icon-Tausch — wenn einer gezeigt werden soll, wird das explizit gebrieft. |
| Feste Varianten überspringen | Variant-Properties mit nur **einem möglichen Wert** (keine echte Auswahl) sind intern/strukturell und werden in der Overview übersprungen. Z. B. "Variant=Tree", "Tree=True", "Width=Full" wenn das die einzigen Optionen sind. |
| Interaction States | Wenn die Komponente eine `🔀 Interaction States`-Variant-Property hat, **immer alle Werte zeigen** — inklusive "None" (entspricht Disabled). Jeden Zustand zeigen, der im Component Set existiert. |
| Code Connect Properties | Wenn eine Komponente sichtbare Code Connect Properties hat (auf erster Ebene exponiert, vom Designer einstellbar), werden diese mit allen möglichen Werten im Properties-Abschnitt aufgelistet. Titel erhält das `⚙️`-Präfix. Value Slots zeigen nur Textwerte (keine Instanz nötig), es sei denn die Property verändert die Komponente sichtbar. Siehe Abschnitt "Code Connect Properties (⚙️)" oben. |
| Multi-Set Dev Properties | Wenn eine Dev-Property als **separate Component Sets** im Design dargestellt wird (z. B. "Drawer → Right", "Drawer → Left" für die `direction`-Property), wird sie als **sichtbare Variant-Property** (`🔀`) in der Haupt-Overview dokumentiert — nicht als `⚙️`, weil es eine echte Design-Entscheidung ist. Per DB UX MCP (`get_component_props`) den Property-Namen und die möglichen Werte ermitteln. Instanzen aus jedem Component Set als Property Values zeigen. |
| Verschachtelte Slots filtern | Beim Styling von Slots in Instanzen NUR direkte Komponenten-Slots stylen, alle Slots innerhalb verschachtelter Instanzen ausblenden |
| Bedingte Slot-Sichtbarkeit | Wenn ein Slot nur bei einer bestimmten Property-Einstellung sichtbar ist (z. B. `📦 Children` nur bei `Expanded=True`), diese Property auf der Visualisierungsinstanz setzen. Im Value-Label eine Anmerkung ergänzen wie "Children (Group)", um die Zugehörigkeit zur Sub-Variante zu verdeutlichen. |

### Was manuell erledigt werden muss

- **Preview:** Repräsentative Instanzen in Light-/Dark-Slots platzieren
- **Anatomy:** Beschriftete visuelle Aufschlüsselungen erstellen
- **Seitenlayout:** Nach dem Erstellen der Overviews den `db-ux-component-page-layout`-Skill ausführen, um die Seite zu organisieren (Component Sets rechts neben die Overviews verschieben)
- **Examples:** Spezifische Nutzungsmuster dokumentieren
- **Feature Backlog / Out of Scope:** Inhaltliche Entscheidungen

---

## Beispiel: Drawer

### Header
- **Komponentenname:** Drawer
- **State:** Stable (Variante `"State": "Stable"` auf der State-Badge-Instanz setzen)
- **Autor:** lp
- **Link 1:** Documentation
- **Link 2:** Submit Feedback

### Properties

| Property | Layout | Werte | Instanz-Konfiguration |
|----------|--------|-------|----------------------|
| 🔀 Container Size | Vertikal | (Def) Small, Medium, Large, Full, Custom | Variante pro Größe, Rounded=False |
| 🔀 Rounded | Horizontal | (Def) False, True | Standardgröße, Rounded togglen |
| 👁️ Show Backdrop | Horizontal | (Def) True, False | Default-Variante + Boolean togglen |
| 👁️ Show Footer | Horizontal | (Def) False, True | Default-Variante + Boolean togglen |
| 👁️ Show Spacing | Horizontal | (Def) True, False | Default-Variante + Boolean togglen |

### Slots

| Slot | Beschreibung |
|------|--------------|
| 📦 Children | Haupt-Inhaltsbereich des Drawers |

---

## Hinweise

- Die Vorlage ist eine **Instanz** einer externen Library-Komponente — Import über `figma.importComponentByKeyAsync(key)`
- **Wichtig:** Alle Slots in den Vorlagenkomponenten (`Component.Overview`, `Overview.Content.Container`, `Overview.Property.Value`) müssen **standardmäßig leer** sein — sonst erhalten verschachtelte Instanzkinder zusammengesetzte IDs, die nicht navigiert werden können
- Wenn Instanzen per `createInstance()` erstellt und in Slots eingefügt werden, funktioniert `findOne`/`findAll` auf den neu erstellten Instanzen
- Die Plugin API sieht frisch platzierte Instanzen auf Branches möglicherweise nicht sofort (Sync-Verzögerung) — wenn ein Node nicht gefunden wird, braucht er eventuell einen Moment zum Persistieren
- `get_metadata` verwenden, um die Vorlagenstruktur zu lesen, dann `use_figma` zum Schreiben
- State Badge: Über Variant-Property `"State": "Stable"` (oder "Beta", "Concept", "Pre-Release") auf der State-Instanz setzen — nicht nur als Text
- **Komponenteninstanzen in Value Slots niemals skalieren** — Originalgröße verwenden. Ausnahme: Responsive/Layout-Komponenten > 700px können per `resize()` auf eine konsistente feste Größe gebracht werden (siehe Regel zur Instanzgröße).
- Autoren-Kürzel: lp = Leape, si = Sissito, to = Tobi
