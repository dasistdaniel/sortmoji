# 🧩 MojiSort – Entwickler-Handout

> Übergabedokument für die Weiterentwicklung · Stand: Oktober 2026

## 1. Projektüberblick

**MojiSort** ist ein Browser-Sortierpuzzle: 6 Türme mit je 5 Emoji-Slots, 5 Emoji-Sorten à 5 Exemplare. Der Spieler verschiebt zusammenhängende Blöcke gleicher Emojis, bis jeder Turm nur noch eine Sorte enthält.

- Technologie: eine einzige `index.html` (HTML + CSS + JavaScript, kein Framework, keine Build-Tools, keine Abhängigkeiten)
- Läuft offline im Browser und ist direkt für GitHub Pages geeignet
- Sound wird per Web Audio API synthetisiert (keine Audio-Dateien)
- Aktueller Umfang: ca. 690 Zeilen in `index.html`

## 2. Spielregeln (implementiert)

- **Board:** 6 Türme – 5 startgefüllt (je 5 zufällige Emojis) + 1 leerer Hilfsturm
- **Auswahl:** Klick auf einen Turm wählt den obersten zusammenhängenden Block gleicher Emojis aus
- **Zugregel:** Der Block darf auf **jedem** Turm mit freiem Platz abgelegt werden – das Ziel-Emoji muss **nicht** passen (gewollte Design-Entscheidung des Auftraggebers)
- **Teilweise Züge:** Passt der Block nicht ganz, werden so viele Emojis verschoben, wie Platz ist (wie beim Ball-Sort-Puzzle)
- **Blockiert:** voller Turm als Ziel → ungültiger Zug (Wackeln + roter Blitz + Fehlton)
- **Sinnlos-Schutz:** Einen komplett sortierten Turm in den leeren Hilfsturm zu verschieben ist verboten
- **Sieg:** jeder Turm leer ODER voll (5 Slots) mit nur einer Emoji-Sorte

## 3. Code-Architektur

### 3.1 Konstanten (oberster Bereich des `<script>`)

```js
EMOJIS = ["🚀", "🌟", "🍀", "🔥", "🐙"]  // 5 Sorten
CAP = 5        // Slots pro Turm
FILLED = 5     // startgefüllte Türme
TUBES = FILLED + 1
```

Neue Sorten oder andere Größe: nur diese Konstanten anpassen – das Layout skaliert über CSS automatisch mit (Mobile-Breakpoint bei 560 px beachten).

### 3.2 Zustand

```js
columns[]   // Array pro Turm, Index 0 = unten … letzter Index = oben
startState  // Kopie des Start-Deals (für Neustart)
selected    // Index des ausgewählten Turms oder null
moves / history[] / won / doneTubes (Set)
```

### 3.3 Kernfunktionen

| Funktion | Aufgabe |
|---|---|
| `newGame()` | mischt 25 Emojis (Fisher-Yates), verteilt auf 5 Türme, startet Einflug-Animation |
| `topRun(col)` | liefert den obersten zusammenhängenden Block gleicher Emojis `{emoji, count}` |
| `tryMove(from, to)` | Kernlogik: Validierung, Zustandsänderung, Flug-Animation, Sieg-Check |
| `isWin()` | jeder Turm leer oder voll & uniform |
| `render(animCol, opts)` | baut das Board komplett neu auf; `opts.deal` = Einflug, `opts.hideTop` = Slots während des Flugs leer lassen |
| `flyGhost(emoji, fromRect, dstSlot, delay)` | absolutes Overlay-Div, fliegt per CSS-Transition |
| `undo()` | holt den letzten `history`-Eintrag zurück (inkl. Animation-Reset) |
| `celebrate()` | Tanz-Animation + Konfetti + Fanfare + Overlay |

## 4. Animationen & bekannte Stolperfallen

- **Flug-Animation:** Die `getBoundingClientRect()`-Werte der **Quell-Slots müssen vor dem Neu-Rendern** erfasst werden! Nach `boardEl.innerHTML = ""` sind die alten Knoten detached und liefern Nullen → die Emojis fliegen sonst von `(0,0)` ein. *(Dieser Bug war schon einmal drin.)*
- **hideTop-Mechanik:** `render()` lässt die obersten `count` Slots des Zielturms leer; ein `setTimeout` (`FLIGHT + idx * STAGGER`) füllt sie bei Landung. Schnelles Weiterklicken währenddessen ist OK – der Spielzustand ist bereits aktualisiert.
- **Tuning:** `STAGGER = 55 ms`, `FLIGHT = 300 ms` (in `tryMove`). Konfetti: 110 + 80 Partikel (`startConfetti`).
- **Performance:** **Kein `backdrop-filter` auf dem Win-Overlay!** Blur über dem animierten Konfetti-Canvas hat bereits zu Ruckeln geführt. Alle Animationen sind transform-/opacity-basiert.
- **`doneTubes`-Set:** verhindert, dass der Fertig-Blitz (`flash`) bei jedem `render()` erneut auslöst; wird bei Undo/Neustart zurückgesetzt.

## 5. Sound (sfx-Modul)

- Web Audio API – Töne werden per Oszillator + Gain-Hüllkurve synthetisiert (Funktion `tone()`)
- Der `AudioContext` wird erst bei der ersten Nutzer-Interaktion erzeugt (Browser-Autoplay-Regeln)
- Mute-Toggle (🔊/🔇) speichert in `localStorage` unter dem Schlüssel `mojisort-muted`
- Sounds: `select`, `deselect`, `place(n)` (Plop-Kette, Tonhöhe steigt), `error`, `click`, `win` (Fanfare)

## 6. Wichtige Parameter zum Tunen

| Parameter | Ort | Wirkung |
|---|---|---|
| `EMOJIS` | Konstanten | Emoji-Sorten (beliebig erweiterbar) |
| `CAP` / `FILLED` | Konstanten | Turmhöhe / Anzahl gefüllter Türme |
| `STAGGER` | `tryMove()` | Versatz der Flug-Kaskade (55 ms) |
| `FLIGHT` | `tryMove()` | Flugdauer pro Emoji (300 ms) |
| `spawnConfetti(n)` | `startConfetti()` | Konfetti-Menge (110 + 80) |
| `dance .6s` | CSS `.tube.celebrate` | Tempo der Sieges-Tanz-Animation |

## 7. Deployment (GitHub Pages)

1. Repository auf GitHub erstellen und den Ordner-Inhalt (`index.html` + `README.md`) pushen
2. **Settings → Pages → Source:** Branch `main`, Ordner `/ (root)`
3. Nach ca. 1 Minute live unter `https://<username>.github.io/<repo>/`

Kein Build-Schritt, kein `npm install` – Datei ändern, committen, fertig.

## 8. Ideen für die Weiterentwicklung

- Schwierigkeitsgrade: mehr Sorten/Türme, weniger oder keine Hilfstürme
- Löser (Solver) einbauen, um garantiert lösbare Levels zu generieren
- Zug-Limit / Highscore pro Level (localStorage)
- Level-Editor oder vordefinierte Level als JSON
- PWA (Service Worker + Manifest) für Installation aufs Handy
- Animationen per `prefers-reduced-motion` abschaltbar machen (Barrierefreiheit)
- Der H1-Verlaufstitel (shine-Animation) nutzt `background-clip: text` – bei Umbau auf ein anderes Theme beachten

---

*💡 Tipp zum Einstieg: Die komplette Spiellogik liegt in einer IIFE am Ende von `index.html` – die Abschnitts-Kommentare (Spiellogik / Anzeige / Sound / Konfetti) geben eine schnelle Orientierung.*
