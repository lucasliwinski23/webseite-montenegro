# CLAUDE.md

Projekt-spezifische Hinweise für Claude Code. Diese Datei wird automatisch in jeden Kontext geladen.

## Projekt

**Podgorica Sauftour 2026** — Single-page Mobile-Webseite für Männertrip nach Podgorica, Montenegro (24.–26. April 2026).

- **Live-URL:** GitHub Pages (`https://<username>.github.io/podgorica-sauftour/`)
- **Zielgruppe:** Mobile-only, iOS Safari + Chrome Android
- **Status:** Aktive Weiterentwicklung gemäß `PROMPT.md`

## Stack

- **Pure HTML / CSS / JS** — single-file `index.html`, kein Build-Step
- **Leaflet 1.9.4** via unpkg CDN für die interaktive Karte
- **Google Fonts** via fonts.googleapis.com
- **GitHub Pages** für Deployment

## Repo-Struktur

```
index.html                Komplette Single-Page-Site (HTML + embedded CSS + JS)
data/trip-data.json       Alle Trip-Daten (Restaurants, Kneipen, Timeline, …)
assets/                   Bilder (flight_route, map_podgorica als PNG + SVG)
docs/                     PowerPoint-Quelle (22 Slides)
PROMPT.md                 Aktueller Weiterentwicklungs-Prompt
README.md                 Projekt-Übersicht
```

## Arbeitsregeln

- **Inhalte ändern:** Nur `data/trip-data.json` editieren — Site rendert daraus.
- **Single-file bleibt:** Kein Build-System, kein npm, keine Module. CSS und JS embedded in `index.html`.
- **Daten-Loading:** `fetch('./data/trip-data.json')` mit Inline-Fallback für `file://`.
- **Reviews/Beschreibungen:** Bleiben wie sie sind — nicht umformulieren.
- **Bilder/Assets:** Nicht komprimieren oder umbenennen ohne Rückfrage.

## Code-Konventionen

- **CSS-Variables** für alle Farben: `:root` (light) und `[data-theme="dark"]`.
- **Animationen:** Pure CSS Keyframes + IntersectionObserver. Keine JS-Animation-Libraries.
- **Tap-Targets:** Mindestens 44×44 px (iOS-Guideline).
- **Telefonnummern:** Immer als `tel:`-Link.
- **Maps-Links:** Immer in neuem Tab (`target="_blank" rel="noopener"`).
- **Performance-Ziele:** Lighthouse Performance > 85, Accessibility > 95.

## Recherche-Suchparameter (intern, nicht für Website-Copy)

- **Schließungs-Status immer vor Empfehlung verifizieren** (RestaurantGuru / Tripadvisor / Foodbook). Nicht ungeprüft empfehlen.
- **Tourist-Trap-Heuristik** für `touristTrap`-Markierung in `trip-data.json`:
  - Lage > Qualität (zentrale Tourist-Magneten mit Reviews à la "great location, fair food")
  - **Sky-Bars / Rooftop-Bars / Hotel-Restaurants** = Default-Verdacht
  - Polarisierte Reviews (5⭐ + 1⭐ ohne Mitte)
  - Pricing-Transparency-Issues (Pricing-by-Weight ohne Hinweis, versteckte Couvert-/Bread-Charges)
  - Premium-Preise + gemischte Quality-Reviews
- **Methodik-Notizen gehören in Commit-Messages oder CLAUDE.md**, nicht in user-facing Itinerary-Texte.

## Lokal testen

```bash
python3 -m http.server 8000
# dann http://localhost:8000
```

## Deployment

Push auf `main` → GitHub Pages baut automatisch (~1 Min).

## Was nicht tun

- Kein React/Vue/Framework einführen.
- Kein Build-Tool (Vite, Webpack, etc.) hinzufügen.
- Keine externen Icon-Libraries — CSS-Shapes und Unicode reichen.
- Keine destruktiven Git-Operationen ohne Rückfrage (force-push, reset --hard).
