# MojiSort 🧩

Ein kleines Emoji-Sortier-Puzzle, das als statische Seite direkt auf **GitHub Pages** läuft – ganz ohne Build-Prozess.

## Spielprinzip

- 6 Türme: 5 gefüllte Türme mit je 5 Emojis + 1 leerer Hilfsturm
- Verschoben werden kann immer nur ein **zusammenhängender Turm aus gleichen Emojis** (am oberen Ende eines Turms)
- Abgelegt werden darf dieser auf **jeden Turm mit freiem Platz** – das oberste Emoji des Ziels muss nicht passen
- Ziel: Am Ende enthält **jeder Turm nur eine Emoji-Sorte** (5× das gleiche Emoji)

## Steuerung

| Aktion | Ergebnis |
|---|---|
| Turm anklicken | Auswählen (oberster gleichfarbiger Block wird markiert) |
| Ziel-Turm anklicken | Turm dorthin verschieben (auf gleiches Emoji oder in leeren Turm) |
| Nochmal auf den Turm klicken | Auswahl aufheben |
| ↩️ Undo | Letzten Zug zurücknehmen |
| 🔄 Neustart | Gleiches Spiel nochmal |
| 🎲 Neues Spiel | Neues zufälliges Spiel |
| 🔊 / 🔇 | Sound an/aus (Einstellung wird gespeichert) |

Alle Sounds werden direkt im Browser per **Web Audio API** erzeugt – es gibt keine Audio-Dateien, die Seite bleibt eine einzelne `index.html`.

## Lokal testen

Einfach `index.html` im Browser öffnen – mehr braucht es nicht.

## Auf GitHub Pages veröffentlichen

1. Ordner als Repository auf GitHub hochladen (z. B. `MojiSort`)
2. **Settings → Pages → Source: `main` Branch, `/ (root)`** wählen
3. Nach ca. 1 Minute ist das Spiel unter `https://<username>.github.io/MojiSort/` erreichbar

## Struktur

```
MojiSort/
├── index.html   # komplettes Spiel (HTML + CSS + JS)
└── README.md
```
