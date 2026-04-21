# Podgorica Sauftour — Weiterentwicklungs-Prompt

Copy den Block unten komplett in Claude Code (oder Claude Projekte / Desktop) und arbeite im Repo-Root. Alle Daten liegen in `data/trip-data.json`, Referenzbilder in `assets/`, aktuelle Webseite in `index.html`.

---

## 🔽 COPY-PASTE PROMPT (alles zwischen den ─── Linien)

────────────────────────────────────────────────────────────

Ich arbeite an einer Single-Page Mobile-Webseite für meinen Männertrip nach Podgorica, Montenegro, 24.–26. April 2026. Die aktuelle Version liegt in `index.html` im gleichen Verzeichnis. Alle inhaltlichen Daten (Restaurants, Kneipen, Zeitplan, Sightseeing, Souvenirs, Reservierungen, Cheat-Sheet) liegen strukturiert in `data/trip-data.json`. Referenz-Bilder (Flugroute, Karte Podgorica) liegen in `assets/`.

**Bitte nicht komplett neu bauen — die aktuelle `index.html` ist die Basis.** Behalte Struktur, Sektionen, interaktive Map (Leaflet), Timeline, expandable Cards und WhatsApp-Template. Führe folgende Änderungen durch.

### Was bleiben soll

- Gesamte Struktur und Reihenfolge der Sektionen (Hero, Flug, 3-Tage-Tabs, Map, Restaurants, Kneipen, Sightseeing, Souvenirs, Reservierungen, Cheat Sheet)
- Alle Animationen — das macht die Seite lebendig:
  - **Ticker/Marquee** oben im Hero (horizontal scrollender Text)
  - **Flammen-Animation** am unteren Rand vom Hero (CSS-Keyframes, 7 flackernde Flammen mit `mix-blend-mode`)
  - **Live-Countdown** zum Abflug (updated jede Sekunde)
  - **Pulsierender Dot** im "Mission geplant"-Tag
  - **Fliegendes Flugzeug** das horizontal durch die Route-Visualisierung fliegt
  - **Fade-in-on-scroll** via IntersectionObserver
  - **Hover-Effekte** auf Cards (translateY, Border-Highlight)
  - **Warnhinweis-Pulse** auf dem Sonntag/Supermarkt-Warning-Banner
- Alle Leaflet-Map-Funktionen mit farbigen Pins
- `tel:`-Links auf allen Telefonnummern
- Direct Maps-Links zu jedem Spot
- Expandable Restaurant-Cards mit Menü-Auszügen und Reviews
- Copy-Button fürs WhatsApp-Template
- Fixed Bottom-Navigation auf Mobile

### Was geändert werden soll

#### 1. Hell- UND Dark-Mode mit Toggle
- Theme-Toggle oben rechts (fixed, kleines Icon-Button, Sonne/Mond-Switch). Auch in der Bottom-Nav oder als erster Eintrag im Header OK.
- Beide Modi voll durchdesignt — nicht nur Farben invertieren.
- **Default: System-Preference** (`prefers-color-scheme: dark`). User-Wahl in `localStorage` speichern.
- CSS-Variables für alle Farben — eine Set für `:root` (light) und eine für `[data-theme="dark"]`.

**Dark Mode Farbpalette** (leicht abgewandelt vom Original — warmer, weniger aggressiv, mehr Pub/Sportsbar-Feeling):
- `--bg-primary: #141110` (tiefes Kaffeebraun statt reinem Schwarz)
- `--bg-section: #1D1815` (Section-Alt)
- `--bg-card: #252019` (Karten)
- `--border: #3A332C`
- `--text-primary: #F5ECE0` (warmes Cream statt kaltes Weiß)
- `--text-muted: #9E8F80`
- `--accent-primary: #E8843D` (warmer gebrannter Orange, weniger Neon als vorher)
- `--accent-warm: #D4A056` (Bernstein/Bier)
- `--accent-deep: #8B3A1A` (Rostbraun als Deep-Accent statt Blutrot)
- `--accent-info: #4A7A8C` (gedeckter Stahlblau)

**Light Mode Farbpalette**:
- `--bg-primary: #FAF6EE` (warmes Cream/Eggshell, nicht reines Weiß)
- `--bg-section: #F3ECDE` (leicht dunkler)
- `--bg-card: #FFFFFF` (Karten reines Weiß für Kontrast)
- `--border: #D4CDBC`
- `--text-primary: #231B14` (sehr dunkles Kaffee)
- `--text-muted: #6B5E50`
- `--accent-primary: #B86825` (erdiger gebrannter Orange)
- `--accent-warm: #9B6F28` (dunkler Bernstein)
- `--accent-deep: #6B2E15` (Rost)
- `--accent-info: #2E5766` (Dunkles Adria-Blau)

**Beide Modi**:
- Die Flammen-Animation im Hero bleibt in beiden Modi — in Light Mode mit leicht anderer Opacity (~60% statt 85%) damit's lesbar bleibt.
- Der Hero-Hintergrund bleibt dunkel in beiden Modi (ein Akzent-Moment), nur die Content-Sektionen wechseln mit dem Theme.
  - → Damit das Hero-Spektakel funktioniert, aber der Rest der Seite tagsüber lesbar ist.

#### 2. Typografie abgewandelt
- **Display**: Bleibt stark, aber weniger Biker → von **Bebas Neue** auf eine warme, kantige Alternative wechseln. Empfehlung: **Alfa Slab One** oder **Big Shoulders Display** (kantig, maskulin, aber nicht ganz so hart). Als Fallback-Kette Bebas Neue → Impact → sans-serif.
- **Heading**: Bleibt **Oswald** (funktioniert super, ist klassisch Kneipen/Sportsbar-tauglich).
- **Body**: Von Archivo auf **Inter** oder **Satoshi** wechseln (moderner, etwas ruhiger). Oder **IBM Plex Sans**.
- **Mono**: Bleibt **JetBrains Mono** für Zeiten, Koordinaten, Scoreboard-Optik.

#### 3. Leichte Stil-Anpassungen weg vom Biker-Vibe
- Entferne das massive "Mission geplant" als Box mit Border — ersetze durch ein subtileres Label in Smaller-Caps. Den pulsierenden Dot aber behalten.
- Die **Flammen** leicht ruhiger: Opacity von 0.85 auf 0.7 runter, Blur von 4px auf 6px — weicher, wie Kaminfeuer statt Flammenwerfer.
- **Ticker oben** behalten, aber Inhalt etwas cooler: „Männer-Mission aktiviert" statt „Mission aktiv", und Einträge wie „DTM → TGD", „W6 7602", „3 Tage · 8 Restaurants · 7 Kneipen". Raus: die 3× „BIER · BIER · BIER" — zu plakativ.
- Bei den Day-Tabs: die aktive Tab hat aktuell einen Linear-Gradient Orange-to-Blood-Red. Ersetze durch einfache solide `--accent-primary` Fill mit 2px dunklerem Border unten — moderner, weniger Bubblegum.
- **Entferne den Full-Bleed Final-Bar** am Ende mit dem „∞" Wasserzeichen — zu Hot-Sauce-Label. Mache stattdessen:
  - Eine einfache Zentrum-Section mit „Živjeli!" in Display-Font groß, darunter „Montenegrinisch für Prost" in muted, einem dünnen Divider, und den Daten „24.–26. April · 3 Tage · 2 Nächte" nochmal.
- Bei den Restaurant-Cards: aktuell sind die `sc-badge` (FR DINNER · OPTION A) in Volltonfarbe. Mache sie **outlined** (1px Border, transparent Background, farbiger Text) — eleganter.

#### 4. Alles voll klickbar
Die komplette Card soll tap-bar sein und die Details aufklappen — nicht nur der „Details"-Button. Das geht mit `cursor: pointer` auf `.spot-card` und Click-Handler auf die ganze Karte. Buttons bleiben für die spezifischen Actions (Maps, Anrufen, Speisekarte), aber stop-propagation dort.

#### 5. Kleine UX-Verbesserungen
- **Scroll-zu-Tag**: wenn man in der Bottom-Nav auf einen Link klickt, soll die Page smooth dorthin scrollen. Das passiert aktuell schon durch `scroll-behavior: smooth`, aber die Bottom-Nav sollte auch visuell feedback geben (welcher Tab ist gerade aktiv basierend auf Scroll-Position — per IntersectionObserver).
- **Map**: zoom auf Marker-Click. Wenn User auf eine Restaurant-Card klickt und sie im Map-Bereich ist, flyTo() zu diesem Pin.
- **Favoriten-Funktion**: Kleiner Stern in der oberen rechten Ecke jeder Card. Beim Tap wird gelb markiert, in `localStorage` gespeichert. Oben in der Bottom-Nav ein „★"-Link der nur die markierten Spots zeigt (als gefilterte Ansicht).
- **Share-Button** im Footer: nutzt `navigator.share()` (mobile) oder fallback clipboard copy.

### Tech-Anforderungen
- **Single-file HTML** bleibt — kein Build-Prozess. Embedded CSS + JS.
- Daten aus `data/trip-data.json` laden (mit `fetch('./data/trip-data.json')`). Falls das nicht geht (file://), als Fallback inline in einem `<script>` block.
- Leaflet weiter via unpkg CDN.
- Google Fonts weiter via fonts.googleapis.com.
- Muss auch offline funktionieren (außer Map-Tiles und Fonts brauchen Connection).
- iOS Safari + Chrome Android testen mindestens gedanklich.

### Akzeptanz-Kriterien
- [ ] Theme-Toggle funktioniert, merkt Wahl, respektiert System-Preference default
- [ ] Hell-Modus ist wirklich lesbar (nicht nur Farben invertiert)
- [ ] Alle Animationen laufen in beiden Modi
- [ ] Alle Telefonnummern sind `tel:`-Links
- [ ] Alle Maps-Links öffnen in neuem Tab
- [ ] Bottom-Nav highlighted aktiven Section-Link
- [ ] Ganze Cards tappable, Buttons mit stopPropagation
- [ ] Favoriten-Feature funktioniert mit localStorage
- [ ] Lighthouse Performance > 85, Accessibility > 95
- [ ] Keine Console-Errors

Wenn du Fragen hast zu den Inhalten, schau in `data/trip-data.json` — dort stehen alle Details strukturiert. Lass Beschreibungen und Review-Zitate so wie sie sind.

────────────────────────────────────────────────────────────

## Zusätzliche Hinweise für dich (Luca)

### Was im Repo drin ist
```
podgorica-sauftour/
├── index.html                           # Die aktuelle Single-Page-Website
├── data/
│   └── trip-data.json                   # Alle Restaurants, Kneipen, Zeitplan, etc.
├── assets/
│   ├── flight_route.png/svg             # Europa-Karte mit Flugroute DTM→TGD
│   └── map_podgorica.png/svg            # Stadtzentrum-Karte mit allen Spots
├── docs/
│   └── podgorica_sauftour_2026.pptx     # Die ursprüngliche PowerPoint (22 Slides)
├── README.md                            # Projektübersicht
└── PROMPT.md                            # Diese Datei
```

### Workflow mit Claude Code
1. Repo auf GitHub erstellen (leer), lokal klonen.
2. Bundle in das Repo entpacken.
3. `claude code` im Ordner starten.
4. Block oben copy-pasten in Claude Code.
5. Claude editiert `index.html` direkt.
6. Mit `git diff` prüfen, committen.

### GitHub Pages Deployment
Damit die Seite live geht (kostenlos unter `https://<user>.github.io/podgorica-sauftour/`):
1. Repo Settings → Pages
2. Source: `main` branch, root
3. Save → nach ~1 Min live

### Manuelle Änderungen an Daten
Alle Trip-Daten (Restaurants, Kneipen, Öffnungszeiten, Telefonnummern, Menü-Preise, Reviews) stehen in `data/trip-data.json`. Zum Ändern einfach das JSON editieren — der Code rendert daraus.

### Open in Maps Apps
Die Maps-Links verwenden `https://maps.google.com/?cid=...` — das öffnet auf iOS Safari automatisch Apple Maps wenn man will, bzw. Google Maps sonst. Falls gewünscht kann der Prompt um `geo:`-URLs erweitert werden (funktionieren direkt auf Android).

### Tipp: Screenshot-Test
Im Prompt kann man ergänzen: „Am Ende mache einen Playwright-Screenshot der Seite auf Mobile-Viewport und zeig ihn mir."
