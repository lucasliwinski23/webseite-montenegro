---
name: restaurant-research
description: Strukturierte Recherche für Restaurant-, Bar-, Brunch- und Café-Empfehlungen. Use when the user asks for new dining/drinking spots, lunch/dinner suggestions, alternatives to existing venues, or wants to extend a trip plan (`data/trip-data.json`). Stellt erst Klärungsfragen (Küche, Standort, Distanz, Anlass, Diät, Anzahl Optionen), recherchiert über Tripadvisor/RestaurantGuru/Foodbook/Wanderlog, verifiziert Schließungs-Status live, evaluiert Tourist-Fallen-Risiko nach 5 Heuristiken, und gibt eine ranked Shortlist mit `touristTrap`-Flags zurück. Im Trip-Kontext kann der Skill Ergebnisse direkt in `data/trip-data.json` einbauen (Restaurant-Card + Itinerary-Option + analytics + reservations).
---

# Restaurant-Research Skill

Strukturierter, mehrstufiger Recherche-Workflow für Dining-/Drink-Empfehlungen. Vermeidet die zwei häufigsten Failure-Modes (geschlossene Spots empfehlen + Touri-Fallen verkaufen).

## Wann anwenden

- User fragt nach einem Restaurant / Bar / Brunch / Café / Pub
- User will eine neue Option in einen bestehenden Plan einbauen
- User will Backup-Optionen für bestehende Auswahl
- User will eine Empfehlung ersetzen (z.B. weil die alte geschlossen ist)
- Trigger-Phrasen: "such mal", "recherchier", "wo gehen wir hin", "gibt's was Besseres", "alternative", "noch was Leichtes", "Restaurant für heute Abend"

## Workflow

### Step 1 · Parameter-Klärung

**Bevor irgendwas recherchiert wird:** offene Klärungsfragen stellen. Wenn der User die Antworten schon im Prompt geliefert hat, überspringen.

Frage nach **AskUserQuestion** (oder direkt in Klartext, wenn nur 1-2 Punkte fehlen):

1. **Küche / Richtung** — eine oder mehrere? (Italian, Mediterran, Konoba/Traditionell, Asian, Healthy/Light, Steakhouse, Brunch, Pub-Food, Bar/Drinks)
2. **Anlass** — Frühstück / Brunch / Mittag / Dinner / Late-Night / Bar
3. **Standort-Referenz** — von wo aus messen wir Distanz? Default ist die Wohnung (in Trip-Kontexten in `data/trip-data.json` unter `meta.apartment.coords`)
4. **Max-Distanz** — Fußweg-Minuten oder Meter (z.B. "≤5 Min", "≤500 m", "egal mit Taxi")
5. **Preis-Range** — €, €€, €€€ (oder konkret: "günstig", "Mittelklasse", "fine dining")
6. **Spezielle Anforderungen** — leicht/schwer, vegetarisch, kein Fisch, glutenfrei, Hangover-tauglich, mit Kindern, Reservierung möglich
7. **Anzahl Optionen** — wie viele willst du? Default: 3-4 ranked
8. **Eintragen in `data/trip-data.json`?** — Ja / Nein / "erst zeigen"

**Wichtig:** Wenn der User explizit sagt "such einfach" oder "ist mir egal", nicht alle Fragen runterrasseln — sinnvolle Defaults annehmen und kurz in Klartext bestätigen.

### Step 2 · Recherche

**Parallele Suchen** (mehrere `WebSearch` in einem Tool-Block):

- Tripadvisor: `"<cuisine>" Podgorica <area> reviews rating ranking`
- RestaurantGuru: `"<spot name>" Podgorica menu reviews`
- Wanderlog: `best <category> in <city> <constraints>`
- Foodbook.me / podgorica.travel (lokal kuratiert)
- Instagram für Atmosphäre / Fotos (über Web-Search nach `instagram.com`)

**Quality-Schwelle:** mindestens **2 unabhängige Quellen** pro Empfehlung mit übereinstimmender Aussage zu Rating + Adresse + Status.

### Step 3 · Live-Verifikation (Closure-Check)

**Pflicht vor Empfehlung** — sonst empfiehlst du wieder einen geschlossenen Laden (siehe Republic Of Good Food Disaster):

- WebSearch: `"<spot name>" closed permanently 2025 2026 still open`
- Check: Letztes Review-Datum auf RestaurantGuru / Tripadvisor — wenn älter als 6 Monate: Verdacht
- Check: Aktive Social-Media-Posts (Instagram/Facebook) im aktuellen Quartal
- Check: "Geschlossen"-Tags auf Maps / Snapchat / Foursquare

**Wenn unsicher:** Spot rausnehmen ODER explizit als "Status unklar — vor Hingehen anrufen" markieren. **Nie blindlings empfehlen.**

**Verifikation passiert intern.** Das Wort "verifiziert" gehört NICHT in user-facing Itinerary-Texte oder Beschreibungen — das ist Selbstverständlichkeit, kein Marketing-Punkt.

### Step 4 · Tourist-Trap-Heuristik

Für jeden Spot der Shortlist die 5 Signale durchgehen. **Ab 2 Treffern → `touristTrap: true` mit Begründung.**

| # | Signal | Indikator |
|---|--------|-----------|
| 1 | **Lage > Qualität** | Direkt am Hauptplatz / berühmten Sight; Reviews wie "great location, fair food", "go for the view, not the food" |
| 2 | **Hotel-Restaurant** | Im Hotel-Foyer / -Lobby. Default-Verdacht (gefangener Kundenstamm) |
| 3 | **Sky-Bar / Rooftop** | "Rooftop", "Sky", "Panorama"-Marketing. Default-Verdacht |
| 4 | **Polarisierte Reviews** | Stark gespalten (5⭐ vs 1⭐ ohne Mitte). Echte gute Spots pendeln sich auf 4.0-4.7 ein |
| 5 | **Pricing-Transparency-Issues** | Pricing-by-Weight ohne Hinweis, Couvert-/Bread-Charges, "6€ wurde 24€ auf der Rechnung" |
| Bonus | **Premium + gemischte Quality** | Gute Avg-Bewertung *aber* "exorbitant teuer für was du bekommst" in mehreren Reviews |

**Wichtig:** Tourist-Trap-Flag heißt **nicht** "rauswerfen". Heißt: User informieren, damit er bewusst entscheidet. Manche Touri-Fallen sind trotzdem gut — der Trap-Tag warnt nur vor dem Pricing/Quality-Risk.

### Step 5 · Output-Format

**Zwei Modi je nach User-Antwort auf Frage 8:**

#### Modus A — "erst zeigen" (Default)

Tabelle in der Antwort:

```
| # | Spot | Rating | Distanz | Preis | Touri-Trap? | Highlights |
|---|------|--------|---------|-------|-------------|------------|
| 1 | …    | 4.6/X  | 5 Min   | €€    | nein        | …          |
| 2 | …    | 4.4/Y  | 8 Min   | €     | ⚠ ja: …    | …          |
```

Pro Spot 1-2 Zeilen Begründung warum gerankt. Bei Touri-Falle: 1 Satz Reason.

Sources-Block am Ende mit den Tripadvisor / RestaurantGuru Links.

#### Modus B — "direkt eintragen"

In `data/trip-data.json` einbauen mit voller Card:

```json
{
  "name": "...",
  "role": "...",
  "category": "brunch | dinner | sportsbar | craft | ...",
  "rating": "4.6",
  "reviews": "126",
  "cuisine": "...",
  "price": "€€",
  "phone": "+382 ...",
  "phoneRaw": "+382...",
  "maps": "https://maps.google.com/?q=...",
  "coords": { "lat": ..., "lng": ... },
  "menuUrl": "...",
  "menuLabel": "Tripadvisor | Website | ...",
  "description": "...",
  "reviewQuotes": [ { "text": "...", "author": "Tripadvisor", "lang": "en" } ],
  "menu": [ { "title": "...", "items": [ ["dish", "~12 €"] ] } ],
  "hours": "Mo-So 8:00 – 23:00",
  "badge": "...",
  "badgeClass": "alt | neon",
  "touristTrap": true,
  "touristTrapReason": "..."
}
```

**Auch updaten** (wenn Eintrag aktiv genutzt werden soll):
- `analytics.restaurants` (oder `.bars`) — eine Zeile Coords + Rating für Map/Donut
- `reservations` — wenn Telefon vorhanden und Reservierung sinnvoll
- Itinerary `items[*].options` — wenn der Spot in eine Tagesentscheidung gehört

**Niemals** "verifiziert offen" oder ähnliche Methodik-Disclaimer in user-facing Description-Texte schreiben — das gehört in Commit-Messages.

### Step 6 · Commit + Merge

Wenn Daten/Code geändert wurden:

1. Commit auf Feature-Branch mit aussagekräftiger Message (Recherche-Quellen, Heuristik-Treffer, was eingebaut wurde)
2. Push Feature-Branch
3. Wenn der User sagt "auf main mergen" / "merge auf main" / "push direkt auf main": `git checkout main && git pull && git merge --no-ff <feature> -m "..." && git push origin main && git checkout <feature>`

## Edge Cases

- **Kein passender Spot gefunden** — ehrlich sagen, nicht erfinden. Lieber Heuristik anpassen ("Distanz aufweichen?") als ungeprüft empfehlen.
- **Tourist-Trap, aber User will trotzdem hin** — Flag drauf, aber empfehlen. User entscheidet.
- **Spot existiert, aber nicht auf üblichen Quellen** — kleines lokales Lokal? Über lokale Quellen (podgorica.travel, montenegropulse.com) gegenchecken.
- **User hat Veto auf eine Küchenrichtung** (z.B. "kein Fisch" wegen Felix) — explizit in Itinerary-Detail-Text und Backup-Beschreibungen erwähnen, damit's beim nächsten Mal nicht wieder passiert.

## Anti-Pattern (Failure Modes vermeiden)

- ❌ Restaurant empfehlen ohne Closure-Check → siehe Republic Of Good Food
- ❌ Tourist-Trap-Flag setzen ohne Begründung → der Punkt ist die Begründung, nicht das Label
- ❌ "Verifiziert"-Sprache in user-facing Texte → das ist Suchparameter-Hygiene, nicht Marketing
- ❌ Optionen-Wahl in der Itinerary ohne Touri-Trap-Chip auf den Option-Cards → User muss das auch beim Auswählen sehen, nicht erst in der Restaurant-Sektion
- ❌ Sky-Bars / Rooftops blindlings als "geile Atmosphäre" empfehlen → erst Reviews durchgehen, oft Touri-Falle
- ❌ Vegetarisch / "Felix-Veto" / Diät-Constraints vergessen wenn der User die schon einmal erwähnt hat
