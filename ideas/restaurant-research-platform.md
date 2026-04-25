# Idee · Restaurant-Research-Plattform

**Status:** Brainstorm — nichts umgesetzt. Kein Bestandteil dieser Trip-Site.

## Kerngedanke

Aus dem `restaurant-research`-Skill (siehe `.claude/skills/restaurant-research/SKILL.md`) eine eigenständige Web-Plattform machen: pre-kuratiertes, verifiziertes und Touri-Fallen-klassifiziertes Restaurant-Wissen pro Stadt — abrufbar über ein simples UI.

Statt dass jeder User für jede Stadt einzeln den Recherche-Workflow neu anstoßen muss, wird die Arbeit **einmal pro Stadt vorbereitet** und dann mehrfach abgerufen.

## User-Flow (UI-Skizze)

1. User wählt **Stadt** (Dropdown / Suche)
2. User wählt **Richtung(en)** (Multi-Select: Italian, Mediterran, Konoba, Healthy, Steakhouse, Brunch, Pub, Bar, …)
3. User wählt **Anlass** (Brunch / Mittag / Dinner / Late-Night / Bar)
4. Optional: **Distanz vom Standort** (Adresse / Stadtviertel / Pin auf Karte)
5. Optional: **Constraints** (vegetarisch, kein Fisch, leicht/Hangover, Preis-Range, mit Kindern)
6. → Filter-Ergebnis: ranked Liste mit Karte, Touri-Fallen-Flags, Reservierungs-Telefon

## Datenpipeline (Backend)

Die Skill-Logik wird zur scheduled Pipeline:

- **Pro Stadt** ein Datensatz (analog zu `data/trip-data.json`, aber als Datenbank)
- **Crawl-Job** pro Stadt (z.B. monatlich): durchläuft Skill-Workflow
  - Quellen-Sweep (Google Maps, Tripadvisor, RestaurantGuru, Foodbook, Reddit, YouTube …)
  - Closure-Check pro Spot (alte Einträge re-verifizieren)
  - Tourist-Trap-Heuristik (5 Signale + Bonus)
  - Normalisierung in einheitliches Schema
- **Diffs** zur Vorperiode tracken (neu eröffnet / geschlossen / Rating-Drift)

## Datenschema (analog zu existierenden Restaurant-Cards)

```jsonc
{
  "city": "Podgorica",
  "spots": [
    {
      "name": "...",
      "category": "brunch | dinner | bar | ...",
      "cuisine": [...],
      "rating": 4.6,
      "reviews": 126,
      "rankInCity": 11,
      "rankTotal": 280,
      "price": "€€",
      "address": "...",
      "coords": { "lat": ..., "lng": ... },
      "phone": "...",
      "hours": { "mon": "...", "tue": "...", ... },
      "menuUrl": "...",
      "instagramUrl": "...",
      "mapsUrl": "...",
      "touristTrap": true,
      "touristTrapReasons": ["hotel-restaurant", "polarized-reviews"],
      "lastVerified": "2026-04-25",
      "sources": ["tripadvisor", "restaurantguru", "google-maps", "foodbook", "reddit"],
      "reviewQuotes": [...],
      "menuExcerpt": [...],
      "specialNotes": "Felix-Veto: Fisch · keine Reservation Sonntag"
    }
  ]
}
```

## Tech-Stack-Optionen (offen)

- **Static-First:** Pipeline generiert pro Stadt eine JSON, Frontend ist Pure HTML/CSS/JS (analog zu dieser Trip-Site). Hosting: GitHub Pages / Cloudflare Pages. Kosten ~0.
- **API-First:** Postgres + simple API + React/Vue/Svelte. Mehr Aufwand, aber Such-/Filter-UX besser bei vielen Spots.
- **Hybrid:** Static JSON pro Stadt + Edge-Function für Geo-Search / Distanz-Sortierung.

Recherche-Pipeline würde initial weiter die Claude-API mit dem Skill nutzen; später ggf. eigene Crawler für die Pflicht-Sweep-Quellen.

## Differenzierung gegen bestehende Tools

- **Tripadvisor / Google Maps:** zeigen alles, ohne Touri-Fallen-Filter. Wir filtern und kategorisieren.
- **Reise-Blogs:** statisch, schnell veraltet, keine systematische Verifikation. Wir re-verifizieren periodisch.
- **Wanderlog / The Fork:** kuratiert, aber kein Touri-Fallen-Signal, keine Multi-Source-Cross-Validation.

USP: **systematische Touri-Fallen-Erkennung + monatliche Re-Verifikation**.

## Offene Fragen

- Monetisierung? (Free + Premium-Cities? Affiliate Reservation? Sponsored aber transparent?)
- Wie viele Städte initial? (1-2 für MVP — z.B. Podgorica, Berlin, ggf. Lissabon)
- User-generated Touri-Fallen-Reports zulassen?
- Mehrsprachig? (DE / EN minimal)
- Mobile-first wie Trip-Site, oder Desktop-tauglich?

## Nächste Schritte (wenn jemals umgesetzt)

1. MVP: 1 Stadt (Podgorica), 30-50 Spots, Static JSON + simples Filter-UI
2. Datenpipeline manuell anstoßen (cron + Skill via Claude API)
3. Schema validieren mit echten Reisenden
4. Bei Resonanz: 2. Stadt, dann automatisierter Crawl

## Bezug zu diesem Repo

- Diese Trip-Site (`index.html` + `data/trip-data.json`) ist **Prototype / Proof-of-Concept** für das Datenschema und das UI-Pattern (Restaurant-Cards mit Touri-Fallen-Banner, Itinerary-Optionen mit Touri-Trap-Chip).
- Der Skill `.claude/skills/restaurant-research/SKILL.md` ist der **Recherche-Workflow**, der zur Pipeline werden würde.

— notiert am 2026-04-25
