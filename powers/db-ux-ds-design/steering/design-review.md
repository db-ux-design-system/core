# DB UX Design System – Figma Library- & Komponenten-Review


Dieses Steering bietet eine strukturierte Checkliste für das Review der DB UX Design System Figma-Libraries auf zwei Ebenen:

1. **Library-Ebene** — Dateistruktur, Seitenorganisation, Namenskonventionen
2. **Komponentenebene** — Interne Qualität einer Komponente (Abstände, Farben, Naming, Struktur)

Es dient zur Überprüfung, ob die Library den in `db-ux-design-library.md` definierten Konventionen entspricht.

## Anwendung

Wenn ein "Design Review" angefragt wird, die folgenden Prüfungen gegen den angegebenen Scope ausführen:
- **Library-Review** — prüft Abschnitt A (Library-Struktur)
- **Komponenten-Review** — prüft Abschnitt B (interne Komponentenqualität) für die angegebenen Seiten/Komponenten
- **Vollständiges Review** — beides

Ergebnisse gruppiert nach Kategorie und sortiert nach Schweregrad berichten. Nichts korrigieren, sofern nicht explizit gewünscht.

**Wichtig:** Vor dem Report immer den aktuellen Stand aus Figma neu lesen. Nicht auf gecachte Daten aus früheren Gesprächsverläufen verlassen.

---

## A. Library-Struktur

### A1. Seitenorganisation

| Prüfung | Regel |
|---------|-------|
| Komponentenseiten | Jede Komponente hat eine eigene dedizierte Seite |
| Seitenbenennung | Seitenname entspricht dem Komponentennamen (inkl. Maturity-Suffix wenn zutreffend) |
| Maturity-Suffix | Seiten für nicht-stabile Komponenten enthalten das Suffix `(Beta)` oder `(Pre-Release)` |
| Deprecated-Kennzeichnung | Veraltete Seiten/Komponenten sind mit `❌` vorangestellt |
| Seitenreihenfolge | Seiten sind logisch organisiert (alphabetisch oder nach Kategorie) |
| Trennseiten | `---`-Seiten existieren als visuelle Trenner zwischen logischen Abschnitten |

### A2. Pflichtseiten

| Prüfung | Regel |
|---------|-------|
| Cover-Seite | `🖼 Cover`-Seite existiert als erste Seite |
| Changelog | `📋 Changelog`-Seite existiert |
| Helper-Seite | `🛟 Helper`-Seite existiert (für geteilte Hilfskomponenten) |
| Code Connect-Seite | `⚙️ Code Connect`-Seite existiert |

### A3. Seiteninhalt-Struktur

| Prüfung | Regel |
|---------|-------|
| Sections | Komponentenseite verwendet Figma Sections zur Gruppierung von Varianten (nach Größe, Typ etc.) |
| Section-Benennung | Sections verwenden beschreibende Namen: Größenvarianten `(Def) Medium`, `Small`, oder Typ-Gruppierungen |
| Subcomponents-Section | Falls Subkomponenten existieren, sind sie in einer `↳ Subcomponents`-Section gruppiert |
| Varianten-Frames | Variantenmatrizen befinden sich in benannten Frames nach dem Muster `Component → Variant` |

### A4. Publishing

| Prüfung | Regel |
|---------|-------|
| Publizierte Komponenten | Hauptkomponenten und Subkomponenten sind publiziert (nicht aus der Library ausgeblendet) |
| Hilfskomponenten ausgeblendet | Hilfskomponenten (`.`-Präfix) sind NICHT in der Library publiziert |
| Beschreibungen | Publizierte Komponenten haben eine Beschreibung mit mindestens einem Dokumentationslink |
| Dokumentationslink | Beschreibung enthält einen Link zur Design-System-Doku-Seite |

---

## B. Interne Komponentenqualität

### B1. Abstände & Tokens

Alle FRAME-, SLOT- und COMPONENT-Nodes scannen (Kinder von INSTANCEs überspringen).

| Prüfung | Regel |
|---------|-------|
| Gap ohne Token | `itemSpacing > 0` ohne `boundVariables.itemSpacing` → sollte 0 oder Token-gebunden sein |
| Padding ohne Token | `paddingTop/Bottom/Left/Right > 0` ohne entsprechende `boundVariables` → sollte 0 oder Token-gebunden sein |
| Eckenradius ohne Token | `topLeftRadius > 0` (etc.) ohne `boundVariables` → sollte Token-gebunden sein |
| Strichstärke ohne Token | `strokeTopWeight > 0` ohne `boundVariables` → sollte Token-gebunden sein |
| Lokale Variablen | An Nodes gebundene Variablen sollten aus der **Core Foundation Library** stammen, nicht lokal definiert sein. Jede lokale Variablennutzung melden — das sind Ausnahmen, die explizit bestätigt werden müssen. |

### B2. Farben & Fills

| Prüfung | Regel |
|---------|-------|
| Hartcodierte Füllfarbe | Solid Fill ohne `fill.boundVariables.color` (Weiß `rgb(255,255,255)` und vollständig transparent ignorieren) |
| Hartcodierte Stroke-Farbe | Solid Stroke ohne `stroke.boundVariables.color` |
| Hartcodierte Textfarbe | Text-Node-Fills ohne `boundVariables.color` (Schwarz `rgb(0,0,0)` als Standard ignorieren) |

### B3. Naming

| Prüfung | Regel |
|---------|-------|
| Generische Namen | Treffer auf `Frame \d+`, `Rectangle \d+`, `Group \d+`, `Vector`, `Line \d+`, `Image \d+` |
| Kopier-Suffixe | Name endet mit ` copy`, ` Copy` oder nachgestellter Zahl, die auf Duplikation hindeutet |
| Ehemalige Slots | Node hat `📦` im Namen, aber `type !== "SLOT"` → Emoji sollte entfernt werden |
| Doppelter Container | Ein Node namens "Container" enthält direkt ein weiteres Kind namens "Container" |
| Kleingeschriebene Frame-Namen | FRAME/SLOT beginnt mit Kleinbuchstabe (Ausnahmen: mit `↳` oder Emoji vorangestellt) |
| Sehr kurze Namen | FRAME/SLOT mit Name ≤ 2 Zeichen |

### B4. Slot-Integrität

| Prüfung | Regel |
|---------|-------|
| Slot-Sizing | SLOT-Nodes sollten `layoutSizingVertical: "FILL"` und `layoutSizingHorizontal: "HUG"` haben (außer die Layout-Richtung des Parents erfordert anderes). **Hinweis:** Versteckte/eingeklappte Slots können als FIXED height angezeigt werden — das ist ein Figma-Darstellungsartefakt. Nur Slots melden, die tatsächlich sichtbar und dennoch FIXED sind. |
| Slot-Sichtbarkeits-Properties | Versteckte Slots sollten eine zugehörige `👁️ Show ...`-Boolean-Property haben |

### B5. Code Connect

> **Hinweis:** Code Connect ist noch nicht in allen Komponenten integriert. Die Prüfungen "Platzierung" und "Erstes Kind" für Komponenten überspringen, bei denen CC absichtlich fehlt. Wenn CC jedoch existiert, immer die Größe auf exakt 0×0 korrigieren.

| Prüfung | Regel | Auto-Fix |
|---------|-------|----------|
| Platzierung | `.⚙️ Code Connect` muss das **erste Kind** jeder Komponentenvariante sein | — |
| Größe | Muss exakt `0×0` px sein | ✅ Immer auf 0×0 resizen, wenn eine Abweichung gefunden wird |
| Position | Muss absolut positioniert sein (nicht im Auto-Layout-Flow) | — |
| Gesperrt | Muss `locked: true` haben | — |

### B6. FloatingContainer

| Prüfung | Regel |
|---------|-------|
| Platzierung | Muss das **zweite Kind** sein (Index 1, nach Code Connect) oder in einem Frame direkt unter CC |
| Constraints | `STRETCH` horizontal + `STRETCH` vertikal |
| Sichtbarkeit | `visible: false` standardmäßig |
| Gesperrt | `locked: true` |
| Größe | Sollte den Varianten-Abmessungen entsprechen |

### B7. Icon-Muster

| Prüfung | Regel |
|---------|-------|
| Struktur | Icons müssen folgen: `Icon Container > Icon Color + Icon Component > <Icon Instance>` |
| Icon Color | Muss ein Shape-Node sein (RECTANGLE o. ä.) mit einer Fill-Color-Variable |
| Icon Component | Muss ein FRAME sein, der eine Icon-Instanz enthält |

### B8. Layer-Struktur

| Prüfung | Regel |
|---------|-------|
| Leere sichtbare Frames | FRAME/SLOT mit `children.length === 0` und `visible !== false` (Hinweis: leere Slots als Platzhalter sind OK) |
| Konsistente Benennung über Varianten | Gleiche strukturelle Layer in einem Component Set sollten in jeder Variante identische Namen haben |
| Layer-Reihenfolge | Innerhalb jeder Variante eines Sets sollten strukturelle Layer in derselben Reihenfolge erscheinen |

### B9. Properties

> Property-Konventionen sind in `db-ux-design-library` § 2 definiert. Diese Prüfungen validieren die Einhaltung:

| Prüfung | Regel |
|---------|-------|
| Property-Emoji-Präfix | Varianten verwenden `🔀`, Toggles verwenden `👁️`, Slots verwenden `📦`, Icon Swaps verwenden `🔄`, Text-Overrides verwenden `✏️` (siehe `db-ux-design-library` § 2.2) |
| Default-Wert-Format | Standardwerte beginnen mit `(Def)` |
| Veraltete Präfixe | Properties sollten NICHT `🎨` oder `💻` verwenden (veraltet) |

### B10. Accessibility

| Prüfung | Regel |
|---------|-------|
| Screenreader-Text-Größe | `Text Screenreader` / `Label Screenreader`-Nodes müssen `0×0` px sein |
| Screenreader-Position | Muss absolut positioniert sein (nicht im Auto-Layout-Flow) |

### B11. Component Overview Vollständigkeit

| Prüfung | Regel |
|---------|-------|
| Overview existiert | Jede Komponentenseite sollte eine `Component.Overview`-Instanz haben |
| Alle Properties dokumentiert | Jede sichtbare `🔀`- und `👁️`-Property des Component Sets ist im Properties-Abschnitt abgebildet |
| Alle Slots dokumentiert | Jeder `📦`-Slot ist im Slots-Abschnitt visualisiert |
| ⚙️ CC Properties aufgelistet | Exponierte Code Connect Properties (auf erster Ebene sichtbar, vom Designer einstellbar) sind dokumentiert |
| Interaction States gezeigt | Wenn die Komponente `🔀 Interaction States` hat, sind alle Werte dargestellt |
| State Badge gesetzt | State Badge entspricht der Maturity der Komponente (Stable/Beta/Pre-Release) |
| Dokumentationslink gesetzt | Link 1 verweist auf die korrekte Docs-URL |

---

## Ausgabeformat

Ergebnisse wie folgt berichten:

```
## Design Review: [Komponenten-/Seitenname]

### ✅ Bestanden
- [Liste der Kategorien ohne Befunde]

### ⚠️ Befunde

#### [Kategoriename] (X Befunde)
| Komponente | Layer | Problem |
|------------|-------|---------|
| ... | ... | ... |
```

Nach Kategorie gruppieren, Kategorien nach Anzahl der Befunde sortieren (meiste zuerst). Repetitive Befunde zusammenfassen (z. B. "📦 Start Slot in allen 9 Varianten" statt 9 einzelne Zeilen).
