# DB UX Design System – Component Page Layout


Dieses Steering hilft beim Organisieren und Aufräumen von Komponentenseiten in der DB UX Core Components Figma-Library. Es regelt die Positionierung, Benennung und Gruppierung von Component Sets, Sections und Frames auf einer Komponentenseite.

## Überblick

Jede Komponentenseite in der Library folgt einer einheitlichen Layoutstruktur. Dieses Steering stellt sicher, dass die Seite nach dem Hinzufügen neuer Varianten, nach Refactoring oder nach dem Erstellen von Overviews gut organisiert und navigierbar bleibt.

## Seitenstruktur (von links nach rechts)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  Overviews          │  Haupt-Component Sets   │  Subcomponents  │  Deprecated │
│  (Component.Overview│  (Sections/Frames)      │  (Section)      │  (Section)  │
│  Instanzen)         │                         │                 │             │
└─────────────────────────────────────────────────────────────────────────────┘
y = 0 (oben ausgerichtet)
```

### Spaltenreihenfolge

| Reihenfolge | Inhalt | Beschreibung |
|-------------|--------|--------------|
| 1 | Component.Overview-Instanzen | Dokumentation (siehe `db-ux-component-overview`-Skill) |
| 2 | Haupt-Component Sets | Die primären Komponentenvarianten (Sections wie "Desktop", "Mobile") |
| 3 | ↳ Subcomponents | Section mit Sub-Component Sets (z. B. Header, Footer) |
| 4 | Component Sets / Standalone Components | Nicht gruppierte Component Sets oder Einzelkomponenten ohne Section |
| 5 | 🛟 Helpercomponents | Section mit internen Hilfskomponenten |
| 6 | ❌ Deprecated | Section mit alten/veralteten Component Sets (immer ganz rechts) |

### Abstandsregeln

| Zwischen | Abstand |
|----------|---------|
| Overviews (horizontal) | 100px |
| Letzte Overview → erste Component Section | 100px |
| Sections (horizontal) | 100px |
| Component Sets innerhalb einer Section (vertikal) | Internes Section-Padding |
| Component Sets innerhalb einer Section (horizontal) | Internes Section-Padding |

## Section-Benennungskonventionen

| Muster | Verwendung | Beispiel |
|--------|------------|----------|
| Einfacher Name | Haupt-Richtungs-/Varianten-Sections | "Desktop", "Mobile" |
| `↳ Subcomponents` | Enthält Sub-Component Sets | "↳ Subcomponents" |
| `❌ deprecated` | Enthält veraltete Component Sets | "❌ deprecated" |

## Component Set Naming

> Die Namenskonventionen sind in `db-ux-design-library` § 1 definiert. Relevante Muster im Kontext des Seitenlayouts:

| Muster | Verwendung | Beispiel |
|--------|------------|----------|
| `ComponentName → Variant` | Hauptkomponente mit Richtung/Typ | "Drawer → (Def) Right", "SHELL → Desktop (Beta)" |
| `↳ ComponentName SUBPART/Variant` | Subkomponente | "↳ Drawer HEADER/(Def) Medium", "↳ Shell CONTENT" |
| `❌ ComponentName → Variant (Deprecated)` | Veraltetes Set | "❌ Drawer → (Def) Right (Deprecated)" |

## Internes Frame-/Section-Layout

### Sections mit mehreren Component Sets

- **Padding:** 80px auf allen 4 Seiten
- **Abstand zwischen Component Sets:** 100px (horizontal und vertikal)
- **Layout-Logik:** Kinder ab Position (80, 80) in einer gitterartigen Anordnung platzieren, mit 100px Abstand zwischen Zeilen und Spalten
- **An Inhalt anpassen:** Nach der Positionierung aller Kinder die Section an ihren Inhalt anpassen: `width = rechte Kante des letzten Kindes + 80`, `height = untere Kante des letzten Kindes + 80`

**Implementierung:**
```js
// Kinder innerhalb einer Section positionieren (standardmäßig vertikal gestapelt)
function layoutSection(section) {
  const padding = 80;
  const gap = 100;
  const children = section.children;
  
  // Vertikal stapeln
  let currentY = padding;
  
  for (const child of children) {
    child.x = padding;
    child.y = currentY;
    currentY += child.height + gap;
  }
  
  // Section an Inhalt anpassen
  const maxRight = Math.max(...children.map(c => c.x + c.width));
  const maxBottom = Math.max(...children.map(c => c.y + c.height));
  section.resizeWithoutConstraints(maxRight + padding, maxBottom + padding);
}
```

### Vertikale Stapelung (einzelne Spalte)

Wenn Component Sets vertikal gestapelt werden (z. B. mehrere Richtungen derselben Komponente):
- Start bei (80, 80)
- Jedes Set unterhalb des vorherigen mit 100px vertikalem Abstand
- Section-Breite = breitestes Kind + 2 × 80

### Grid-Layout (gruppierte Spalten)

Wenn Kinder einer Section ein gemeinsames **Namens-Präfix** teilen (z. B. `Horizontal/...`, `Vertical/...`, `Tree/...`, `Flat Icon/...`), werden sie als **Spalten-Grid** nach Präfix gruppiert angeordnet:

- Jede Gruppe wird zu einer Spalte
- Elemente innerhalb einer Gruppe werden vertikal gestapelt (100px Abstand)
- Spalten werden von links nach rechts platziert (100px Abstand zwischen Spalten)
- Alle Spalten beginnen bei y = Padding (80px)

**Erkennung:** Wenn Kindernamen `/`-Trennzeichen enthalten, wobei das erste Segment sie logisch gruppiert, Grid-Layout verwenden.

**Implementierung:**
```js
function layoutSectionAsGrid(section) {
  const padding = 80;
  const gap = 100;
  const children = [...section.children];
  
  // Nach erstem Pfadsegment gruppieren
  const groups = {};
  for (const child of children) {
    const prefix = child.name.split("/")[0];
    if (!groups[prefix]) groups[prefix] = [];
    groups[prefix].push(child);
  }
  
  // Spalten layouten
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
  
  // An Inhalt anpassen
  const maxRight = Math.max(...children.map(c => c.x + c.width));
  const maxBottom = Math.max(...children.map(c => c.y + c.height));
  section.resizeWithoutConstraints(maxRight + padding, maxBottom + padding);
}
```

### Einzelnes Component Set (ohne Section-Wrapper)

Standalone Component Sets (nicht in einer Section verpackt) werden direkt an der passenden x-Position platziert, oben ausgerichtet.

## Workflow

### Benötigte Eingaben

1. **Komponentenseiten-URL oder -Name** — welche Seite organisiert werden soll
2. **File Key** — welche Figma-Datei

### Schritte

1. **Analysieren** — Seitenstruktur lesen, alle Component Sets, Sections, Frames und Overview-Instanzen identifizieren
2. **Layout vorschlagen** — Vorgeschlagene Organisation präsentieren:
   - Welche Elemente in welche Spalte kommen
   - Nötige Korrekturen bei der Benennung
   - Veraltete Elemente, die verschoben werden müssen
3. **Ausführen** — Nach Bestätigung durch den User:
   - Overviews nach x=0, y=0 verschieben (links)
   - Haupt-Component Sets/Sections rechts neben die Overviews verschieben
   - Subcomponents-Section nach den Haupt-Sets platzieren
   - Deprecated-Section ganz nach rechts verschieben
   - Konsistente Ausrichtung y=0 oben sicherstellen
   - Einheitliche Abstände (100px) einhalten

### Regeln

| Regel | Details |
|-------|---------|
| Alles oben ausrichten | Alle Top-Level-Elemente (Overviews, Sections, Component Sets) beginnen bei y=0 |
| Internes Layout beibehalten | Inhalte innerhalb von Sections nicht umordnen — nur die Sections selbst verschieben |
| Deprecated am Ende | Veralteter Inhalt immer an der rechtesten Position platzieren |
| Subcomponents nach Haupt | Subcomponent-Section kommt immer nach den Haupt-Component-Sections |
| Nichts löschen | Nur umpositionieren — niemals Elemente beim Aufräumen löschen oder ausblenden |
| Kinder sortieren | Innerhalb einer Section Geschwister-Component Sets/Components **alphabetisch nach Name** sortieren, wobei `(Def)`-Einträge immer zuerst kommen. Z. B. "(Def) Top" → "Bottom" → "Flat Icon" |
| Eine Overview pro Komponente | Jede eigenständige Subkomponente bekommt eine eigene Overview. Nicht zusammenhängende Subkomponenten nicht in einer einzigen Overview zusammenfassen, nur weil sie eine Section teilen. Z. B. Primary Actions, Secondary Actions, Meta Navigation und Brand bekommen jeweils eine eigene Overview — auch wenn sie alle in der "↳ Subcomponents"-Section leben. |
| Overview-Reihenfolge | Overviews werden sortiert: Hauptkomponente(n) zuerst, dann Subcomponent-Overviews. Innerhalb jeder Gruppe alphabetisch sortieren. |
| Status-Suffix | Component Sets mit "(Beta)", "(Deprecated)" etc. behalten ihre Suffixe |

## Beispiel: Drawer Seitenlayout

```
x=0:     Component.Overview (Drawer)        [1440px breit]
x=1540:  Component.Overview (Drawer Header) [1440px breit]  
x=3080:  Component.Overview (Drawer Footer) [1440px breit]
x=4620:  Drawer → (Def) Right              [Component Set]
x=5800:  Drawer → Left                     [Component Set]
x=6980:  Drawer → Bottom                   [Component Set]
x=9285:  Drawer → Top                      [Component Set]
x=11590: Section "↳ Subcomponents"          [Header + Footer Komponenten]
x=12170: Section "❌ deprecated"            [Alte veraltete Sets]
```

## Hinweise

- Dieses Steering **nach** dem Erstellen/Aktualisieren von Component Overviews ausführen
- Große Seiten mit vielen Varianten erfordern möglicherweise manuelle Entscheidungen bei der Gruppierung
- Wenn eine Komponente sowohl Desktop- als auch Mobile-Varianten als separate Sets hat, diese in eigenen Sections gruppieren
- Das `db-ux-component-overview`-Steering verweist auf dieses Steering für die abschließende Seitenorganisation
