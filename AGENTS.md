## 0. Überblick: Architektur & Design

**Projektname:** GPA – ProTab Studio | Professional Edition  
**Technologie:**  
- Single-Page-Webanwendung in **rein statischem HTML/CSS/JS**, ausgeliefert über eine einzige Datei: [`index.html`](https://github.com/DaFum/GPA/blob/main/index.html).  
- Kein separates Build-System, kein Backend, kein Bundler. Alle Styles, Logik und UI-Strukturen sind inline in `index.html` organisiert.  
- UI-Framework: handgeschriebenes CSS mit moderner UI (Panels, Dashboard, Heatmaps, Histogramm).  
- Externe Libs (typisch für diese Art Anwendung, aus dem HTML ableitbar):  
  - Google Fonts (`Inter`, `JetBrains Mono`)  
  - Font Awesome 6 Icons  
  - Eine Tab/Score-Rendering-Engine (z.B. AlphaTab), angesprochen über das `#at-container`‑Element.  

**Designprinzip:**  
- „Single File Solution“: Alles – Layout, State-Management, Upload-Handling, Analyse-Visualisierung und Transportsteuerung – ist in einer Datei gekapselt.  
- Klare Trennung von visuellen Zonen im Layout:  
  - **Sidebar** (Upload, Track-Auswahl, Einstellungen)  
  - **Score-Bereich** mit AlphaTab-Rendering (`#at-container`)  
  - **Transport- & Playback-Control-Leiste**  
  - **Analyse-Dashboard** (Tonart, Pitch-Histogramm, Rhythmus-Heatmap, Komplexitätsanzeige u. a.)

Innerhalb dieser Single-File-Architektur sind logisch mehrere „Agents“ erkennbar, die die Aufgaben aufteilen. Diese Datei beschreibt diese Agenten, ihre Zuständigkeiten und Interaktionen.

---

## 1. Agenten-Übersicht

In `index.html` ist (im Script-Bereich) funktional mindestens die folgende Agenten-Aufteilung sinnvoll und faktisch angelegt:

1. **File & Score Agent**  
   - Verantwortlich für: Dateiupload, Drag&Drop, Lesen der Guitar‑Pro/TAB‑Datei, Übergabe an die Tab/Score-Engine.
   - Benutzt u. a.:  
     - `.drop-zone` (Drag&Drop UI)  
     - `<input type="file">` (falls vorhanden)  
     - AlphaTab/Score-Container `#at-container`  

2. **Playback & Transport Agent**  
   - Verantwortlich für: Play, Pause, Stop, Loop, Position, aktuelle Zeit/Bar-Information.  
   - Benutzt u. a.:  
     - Buttons mit Transport-Logik (`.btn-transport`)  
     - Zeitanzeige `.time-display`  
     - Tempo-/Volume‑Slider (`.slider`, z.B. für BPM / Master Volume)  
     - Schnittstelle zur Tab/Score-Engine (Start/Stop, Seek).  

3. **Track Management Agent**  
   - Verantwortlich für: Darstellung und Steuerung der einzelnen Instrumenten-Tracks.  
   - Benutzt u. a.:  
     - `.track-list`, `.track-item`, `.track-icon`, `.track-controls`  
     - Mute-/Solo-Buttons (`.track-btn.mute`, `.track-btn.solo`)  
   - Verknüpft UI-Aktionen mit der zugrundeliegenden Playback-Engine (Mute/Solo setzt z.B. Track-Gain oder Routing).  

4. **Analysis & Theory Agent**  
   - Verantwortlich für: musikalische Analyse und Visualisierung.  
   - Typische Aufgaben, die aus dem Markup ableitbar sind:  
     - Bestimmung / Anzeige der **Tonart** (Key) und Skala  
       - `key-display`, `.key-note`, `.key-scale`  
     - **Pitch-Histogramm**  
       - `#pitch-histogram`, `.bar`, `.bar-label`  
     - **Rhythmus-Heatmap**  
       - `#rhythm-heatmap`, `.heat-cell`  
     - **Komplexitätsanalyse**  
       - `#complexity-circle`, `.complexity-display`  
     - Zusammenfassung und Statistiken in der **Analysis Dashboard UI**  
       - `.analysis-dashboard`, `.dashboard-content`, `.dashboard-grid`, `.stat-card`  

5. **UI & Theme Agent**  
   - Verantwortlich für:  
     - Theme-Umschaltung (Dark/Light) über `data-theme="light"` und CSS‑Variablen (`:root`, `[data-theme="light"]`).  
     - Sidebar-Toggle (`.sidebar-toggle`, `.app-main.sidebar-collapsed`).  
     - Mobile-Layout/Responsive-Verhalten (u. a. `.mobile-menu-toggle`).  
     - Animations- und Accessibility-Aspekte (z.B. `:focus-visible`, `sr-only`).  
   - Stellt sicher, dass alle anderen Agenten konsistent im selben Layout- und Theme-Kontext laufen.

Diese „Agents“ sind aktuell innerhalb einer Datei realisiert, üblicherweise über Funktionsgruppen, Event-Listener und gemeinsame State-Objekte.

---

## 2. Detailbeschreibung der Agents

### 2.1 File & Score Agent

**Rolle:**  
- Einstiegspunkt für Nutzer:innen, die eine Tab- oder Score-Datei laden möchten.  
- Liefert die Grundlage für alle nachfolgenden Agents (Playback, Track, Analyse).

**UI-Anknüpfungspunkte:**  
- `.drop-zone`  
  - Drag&Drop von Dateien (z.B. `.gp`, `.gp5`, `.gp7`, `.gpx`, `.xml`, `.mid`).  
  - Zustand `drag-over` zur visuellen Hervorhebung.  
- Optionales `<input type="file">` (im HTML oder per Script erstellt).  
- Übergabe an den AlphaTab-/Score-Renderer im `#at-container`.

**Typischer Ablauf:**  
1. Nutzer:in zieht eine Datei auf die Drop-Zone oder wählt sie über den File-Dialog.  
2. Agent liest die Datei (z.B. via `FileReader`) und übergibt den Binär-/Textinhalt an die Tab-Engine.  
3. Die Engine rendert Noten/TAB im `#at-container` und liefert Metadaten:  
   - Track-Liste (Instrumente, Stimmen)  
   - Tempo, Taktart, Länge  
   - Pitch-/Event-Daten für die spätere Analyse.  
4. Agent triggert Initialisierung der anderen Agents (Track-List, Transport-Status, Analyse-Preprocessing).

**Single-File-Aspekt:**  
- Die gesamte Logik für Input-Handling und Engine-Initialisierung liegt innerhalb desselben `<script>`‑Blocks in `index.html`.

---

### 2.2 Playback & Transport Agent

**Rolle:**  
- Steuert die globale Wiedergabe des geladenen Scores.  

**UI-Anknüpfungspunkte:**  
- `.transport-controls` mit Buttons:  
  - Play/Pause (`.btn-transport.primary`)  
  - Stop, Rewind, ggf. Loop, Metronom etc.  
- Slider-Gruppen (`.slider-container`):  
  - Tempo/BPM  
  - Master-Volume  
- `.time-display`  
  - Aktuelle Position in Zeit/Bars/Beats.

**Verhalten (Konzeptuell):**  
- Verknüpft Klicks/Events mit der Playback-API der Tab-Engine.  
- Hält internen State:  
  - `isPlaying`, `currentPosition`, `loopPoints` etc.  
- Aktualisiert die `.time-display` periodisch (z.B. via `requestAnimationFrame` oder `setInterval`) während der Wiedergabe.

---

### 2.3 Track Management Agent

**Rolle:**  
- Bietet Kontrolle über einzelne Tracks (Instrumente) des Scores.

**UI-Anknüpfungspunkte:**  
- `.track-list` enthält dynamisch erzeugte `.track-item`‑Elemente.  
- Jede `.track-item` besteht aus:  
  - `.track-icon` (Instrument-Symbol)  
  - `.track-info` mit `.track-name` und `.track-details`  
  - `.track-controls` mit `.track-btn.mute` und `.track-btn.solo`.  

**Verhalten (Konzeptuell):**  
- Initialisiert sich nach dem Laden eines Scores durch den File & Score Agent.  
- Reagiert auf Klicks:  
  - `Mute`: deaktiviert/ reduziert die Lautstärke eines Tracks in der Playback-Engine.  
  - `Solo`: aktiviert genau diesen Track und schaltet andere auf Mute, sofern nicht mehrfach-Solo gewünscht ist.  
- Aktualisiert UI-State (CSS-Klassen `active` usw.) und spiegelt Engine-Status zurück.  

**Responsive-Aspekt:**  
- In `.sidebar-collapsed` werden Track-Details und -Buttons ausgeblendet, um Platz zu sparen. Der Agent muss trotzdem funktional bleiben und mit reduzierter UI arbeiten.

---

### 2.4 Analysis & Theory Agent

**Rolle:**  
- Führt musikalische Auswertungen durch und mappt sie auf eine interaktive Dashboard-Oberfläche.

**UI-Anknüpfungspunkte (aus dem Markup):**  
- **Dashboard-Grundstruktur:**  
  - `.analysis-dashboard` (Overlay)  
  - `.dashboard-content`, `.dashboard-header`, `.dashboard-body`, `.dashboard-grid`, `.stat-card`  
- **Tonart & Skala:**  
  - `.key-display`, `.key-note`, `.key-scale`  
- **Pitch-Histogramm:**  
  - `#pitch-histogram`, darin `.bar` mit `.bar-label` (für Notennamen)  
- **Rhythmus-Heatmap:**  
  - `#rhythm-heatmap`, darin `.heat-cell`  
- **Komplexität:**  
  - `.complexity-display`, `#complexity-circle`  

**Analytische Aufgaben (konzeptionell):**  
- Zählt Pitches über das gesamte Stück oder reduzierte Abschnitte hinaus -> füllt `#pitch-histogram`.  
- Analysiert Notenverteilung/Tonleiter -> leitet wahrscheinlichste Tonart (`Key`) und Skala ab.  
- Erfasst rhythmische Dichte in Takten/Subdivisions -> generiert Heatmap (`#rhythm-heatmap`).  
- Aggregiert verschiedene Metriken (Tonumfang, Taktwechsel, Notendichte, Rhythmusvielfalt) zu einem Komplexitäts-Score -> visualisiert im `#complexity-circle`.

**Interaktion mit anderen Agents:**  
- Nutzt vom File & Score Agent bereitgestellte Daten (Events, Takte, Pitches).  
- Kann optional mit dem Playback-Agent gekoppelt sein (z.B. Live‑Marker im Histogramm zur aktuellen Position).

---

### 2.5 UI & Theme Agent

**Rolle:**  
- Kapselt nicht-fachliche UI-Belange: Layout, Theme, Toggles, Animation, Accessibility.  

**UI-Anknüpfungspunkte:**  
- Theme (Dark/Light):  
  - `data-theme="light"` am `<html>`‑ oder `<body>`‑Element.  
  - CSS-Variablen in `:root` und `[data-theme="light"]`.  
- Layout-Toggles:  
  - `.sidebar-toggle` (für `app-main.sidebar-collapsed`)  
  - `.mobile-menu-toggle` (für mobile Steuerung).  
- Allgemeine Utility-Klassen:  
  - `.sr-only` für Screenreader‑Text, `:focus-visible` für Fokus-Rahmen.  

**Verhalten (konzeptuell):**  
- Reagiert auf Theme-Toggle (z.B. Klick auf Icon) und setzt `data-theme` entsprechend.  
- Speichert evtl. User-Präferenz (z.B. via `localStorage`).  
- Steuert Sidebar-Ein-/Ausklappen:  
  - Toggelt `.app-main.sidebar-collapsed`  
  - Passt damit Breite der Sidebar und Sichtbarkeit von Texten/Controls an.  
- Stellt sicher, dass andere Agents (Playback, Track, Analysis) auf UI‑Events reagieren können, ohne selbst Layout-Logik zu besitzen.

---

## 3. Interaktionsmodell der Agents

### 3.1 Datenfluss (vereinfacht)

```text
[User] 
  └─(Datei)→ [File & Score Agent]
                 ├─Initialisiert→ [Score/Playback Engine + UI Container (#at-container)]
                 ├─liefert→ Track-Metadaten → [Track Management Agent]
                 └─liefert→ Event-/Pitch-Daten → [Analysis & Theory Agent]

[User]
  └─(Play/Pause/Stop, Tempo, Volume)→ [Playback & Transport Agent]
                                        └─steuert→ Score/Playback Engine

[User]
  └─(Track-Mute/Solo/Select)→ [Track Management Agent]
                                 └─steuert→ Score/Playback Engine

[User]
  └─(Analysis öffnen)→ [Analysis & Theory Agent]
                          └─nutzt→ vorverarbeitete Event-/Pitch-Daten

[User]
  └─(Theme/Sidebar/Mobile)→ [UI & Theme Agent]
                              └─passt→ Layout/Theme für alle Agents an
```

### 3.2 Single-File-Constraint

- Alle Agents werden im selben globalen Kontext definiert (z.B. über Namespaces, Klassen oder IIFEs innerhalb eines `<script>`).  
- Kommunikation zwischen Agents erfolgt typischerweise über:  
  - Gemeinsame State-Objekte  
  - Funktionsaufrufe  
  - DOM-Events / Custom Events  
- Wichtig für Wartbarkeit:  
  - Im Code klare Sektionen einziehen, z.B.:  
    - `// Agent: File & Score`  
    - `// Agent: Playback & Transport`  
    - `// Agent: Track Management`  
    - `// Agent: Analysis & Theory`  
    - `// Agent: UI & Theme`

---

## 4. Guidelines für Erweiterungen

Bei jeder Erweiterung sollten folgende Regeln eingehalten werden, um die Single-File-Solution sauber zu halten:

1. **Klare Agent-Zuordnung**  
   - Jede neue Funktion/Feature gehört genau einem Agent.  
   - UI-Elemente immer im Kommentar direkt einem Agent zuordnen.

2. **Kein Inline-Business-Logic in Event-Handlern**  
   - Event-Listener sollten nur auf Agent-Methoden verweisen, z.B.:  
     - `button.addEventListener('click', () => playbackAgent.togglePlay());`

3. **Keine neuen globalen Variablen ohne Namespace**  
   - State in Agent-Objekte kapseln (`const analysisAgent = { state: {...}, init() {...}, ... }`).

4. **Instrumentierung / Debugging**  
   - Optional ein kleines Dev-Panel einführen, das z.B. die wichtigsten Agent-Zustände ausgibt (insbesondere hilfreich, da es keinen Modul-Bundler gibt).

---

## 5. Zusammenfassung

- GPA / ProTab Studio ist als **Single-File-Solution** in `index.html` konzipiert.  
- Trotz fehlender Modulstruktur ist der Code funktional in **mehrere Agents** gegliedert:  
  - File & Score  
  - Playback & Transport  
  - Track Management  
  - Analysis & Theory  
  - UI & Theme  
- Diese Datei dient als referenzierte, „offizielle“ Beschreibung dieser Agenten und soll beim Refactoring, Debugging und bei Erweiterungen als Orientierung dienen.