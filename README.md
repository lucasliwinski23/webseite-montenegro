# Podgorica Sauftour 2026 🍻

Single-page mobile website für den Männertrip nach Podgorica, Montenegro · 24.–26. April 2026.

**Stack:** Pure HTML / CSS / JS · Leaflet für die Karte · Zero build step · GitHub Pages ready.

## Live-Demo

Nach Deployment auf GitHub Pages erreichbar unter:
```
https://<username>.github.io/podgorica-sauftour/
```

## Was drin ist

- **Hero** mit Flammen-Animation, Live-Countdown zum Abflug, Stats-Overview, Ticker-Marquee
- **Flight-Cards** mit animiertem Flieger der durch die Route fliegt
- **3-Tage-Tabs** (Freitag/Samstag/Sonntag) mit Stunde-für-Stunde Timeline
- **Interactive Leaflet-Map** mit allen 22 Spots (Wohnung, Restaurants, Kneipen, Supermärkte, Sights, Souvenirs)
- **8 Restaurants** mit Ratings, Reviews (auf Deutsch), Speisekarten mit Preisen, `tel:`-Links
- **7 Kneipen** (Warmup, Sportsbar, Craft-Bier, Party)
- **4 Sightseeing-Spots** (Sahat Kula, Stara Varoš, Millennium-Brücke, Moraca-Promenade)
- **3 Souvenir-Shops** für Freundinnen-Geschenke
- **Reservierungs-Liste** mit Priorität + Tap-to-Call
- **WhatsApp-Template** auf Englisch mit Copy-Button
- **Cheat Sheet** mit allen Notfall-Infos
- **Mobile Bottom-Nav** mit direkten Scroll-Anchors

## Projekt-Struktur

```
podgorica-sauftour/
├── index.html               Die komplette Single-Page-Site
├── data/
│   └── trip-data.json       Alle Trip-Inhalte (Restaurants, Bars, Zeitplan, ...)
├── assets/
│   ├── flight_route.png     Europa-Karte mit Flugroute DTM→TGD
│   ├── flight_route.svg     SVG-Source
│   ├── map_podgorica.png    Stadtzentrum-Karte mit allen Spots
│   └── map_podgorica.svg    SVG-Source
├── docs/
│   └── podgorica_sauftour_2026.pptx   PowerPoint-Version (22 Slides)
├── README.md
└── PROMPT.md                Claude-Code-Prompt für Weiterentwicklung
```

## Inhalte editieren

Alle Trip-Daten stehen in `data/trip-data.json`. Dort Restaurants, Preise, Telefonnummern, Reviews, Timeline usw. anpassen — die Site rendert automatisch daraus.

## Lokal starten

Zwei Optionen:

**Option A — Einfach die Datei öffnen:**
```bash
open index.html
```
⚠ `fetch()` funktioniert bei `file://` in manchen Browsern nicht — dann Option B.

**Option B — Lokaler Webserver (empfohlen):**
```bash
# Python 3
python3 -m http.server 8000

# Node (wenn du http-server installiert hast)
npx http-server -p 8000
```
Dann im Browser: `http://localhost:8000`

## Deployment auf GitHub Pages

1. Repo auf GitHub erstellen und den Code pushen:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/<username>/podgorica-sauftour.git
   git push -u origin main
   ```

2. GitHub → Repo → **Settings** → **Pages** → Source: `main` branch / `root` → **Save**

3. Nach ~1 Minute ist die Seite live unter `https://<username>.github.io/podgorica-sauftour/`

## Weiterentwickeln

Siehe [PROMPT.md](./PROMPT.md) — enthält einen ausführlichen Copy-Paste-Prompt für Claude Code, um:
- Hell- UND Dark-Mode-Toggle einzubauen
- Den Biker-Stil etwas abzumildern (weniger aggressiv, mehr Pub/Sportsbar-Feeling)
- Alle Animationen zu behalten
- Favoriten-Feature mit localStorage hinzuzufügen
- Bottom-Nav mit Scroll-Spy zu versehen

## Tech-Details

- **Fonts:** Bebas Neue (Display), Oswald (Heading), Archivo (Body), JetBrains Mono (Data) — via Google Fonts
- **Map:** Leaflet 1.9.4 mit CARTO Dark Tiles (via unpkg CDN)
- **Icons:** CSS-shapes und Unicode-Symbole (keine externen Icon-Libraries)
- **Animations:** Pure CSS Keyframes (keine JS-Animation-Libraries) plus IntersectionObserver für Fade-In

## Credits

Daten gesammelt durch ausführliche Recherche zu Podgorica — alle Restaurants/Kneipen sind reale Spots mit aktuellen Google-Ratings und echten Telefonnummern (Stand April 2026). Reviews sind auf Deutsch paraphrasiert (Copyright-safe).

## License

Privat für den Trip. Keine Wiederverwendung ohne Rücksprache.
