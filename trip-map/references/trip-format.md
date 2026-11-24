# trip.json format

One file describes one trip. The engine reads it at every step; you edit it as you learn more.

## Minimal example

```json
{
  "meta": {
    "title": "Noida to Gurugram",
    "eyebrow": "City drive · Car",
    "verb": "driving",
    "region": "Delhi, India",
    "country": "in",
    "summary": "{km} km across south Delhi with {stops} stops worth the detour."
  },
  "days": [
    {"n": 1, "from": "Noida", "to": "Gurugram", "pace_kmh": 24, "note": "Leave after the morning rush."}
  ],
  "stops": [
    {"name": "Noida Sector 18", "type": "start", "day": 1, "q": "Sector 18, Noida",
     "why": "Easy meeting point with parking and fuel.", "tip": "Start after 10:30 to miss office traffic.",
     "has": ["fuel", "food", "atm"]},
    {"name": "Qutub Minar", "type": "view", "day": 1, "q": "Qutub Minar, Delhi",
     "why": "A 73 m tower from 1193 and the ruins around it.", "tip": "Buy tickets online to skip the queue.",
     "has": ["food"], "photo_picks": [8, 0]},
    {"name": "DLF Cyber Hub", "type": "finish", "day": 1, "q": "DLF Cyber Hub, Gurugram",
     "why": "Dozens of restaurants in one place to end the day.", "tip": "Paid parking in the basement.",
     "has": ["food", "atm"]}
  ]
}
```

## meta

| Field | Required | What it is |
|---|---|---|
| `title` | yes | Shown large on the title screen, usually "A to B". |
| `eyebrow` | no | Small label above the title, such as "Road expedition · Motorcycle". |
| `verb` | no | `driving` (default) or `riding`. Used in labels like "riding days". The route always follows roads open to cars, so this skill does not suit walking or off-road cycling routes. |
| `season` | no | Best months, or the date for a trip on a fixed day, shown next to the eyebrow. |
| `notice` | no | A warning that applies to the whole trip, as one string or a list of strings. Shown on the title screen and above the stop list. |
| `region` | no | Added to place lookups for stops without their own `q`, such as "Himachal Pradesh, India". Use a name a map search knows. For a trip across several states, leave it out and give each stop a `q`. |
| `country` | no | Two-letter code that restricts lookups to one country, such as `in`. Leave it out for cross-border trips. |
| `summary` | no | One sentence for the title screen. Tokens `{km}`, `{days}`, `{stops}`, `{pumps}`, `{gap}` (longest stretch without fuel, km) and `{high}` (highest point, m) are replaced with measured values. |
| `theme.accent` | no | Hex colour for the route and highlights, such as `"#ffc000"`. |
| `checked` | no | Date shown in the footer. Defaults to today. |

## days

One entry per travel day, numbered from 1.

| Field | Required | What it is |
|---|---|---|
| `n` | yes | Day number. |
| `from`, `to` | yes | Names shown in the day header. |
| `pace_kmh` | yes | Honest average speed for the day, including traffic and short breaks. Drives every time estimate. |
| `note` | no | One line on what the day is like. |
| `remote` | no | `true` when the day runs through thinly settled country. The engine then also collects stays and food along the road between stops, not only near them. If no day sets this, stretches above 1,500 m are treated as remote. |

## stops

In travel order. The first is `start`, the last is `finish`. The last stop of each day is where the night is spent.

| Field | Required | What it is |
|---|---|---|
| `name` | yes | Shown on the map and the card. |
| `type` | yes | `start`, `finish`, `stay` (night halt), `fuel`, `food`, `view` or `pass`. |
| `day` | yes | The day this stop belongs to. |
| `q` | no | Text for the place lookup, best as "name, city". Defaults to the name plus `meta.region`. If it finds nothing, the engine retries with shorter versions and says so. |
| `lat`, `lng` | no | Exact position. Overrides the lookup. Use only when you are sure. |
| `via` | no | The route passes through every stop by default. Set `false` for a place seen from the road rather than driven to; its pin then sits at the nearest point of the route. |
| `refine` | no | `"peak"` moves the stop to the highest point of the road within 5 km, for mountain passes. |
| `km_hint` | no | Places the stop at this distance from the start, for features with no lookup, such as a plateau or a set of hairpins. |
| `why` | yes | Why this stop is worth making. |
| `tip` | yes | One practical piece of advice. Two short sentences are fine when both matter. |
| `about` | no | One or two sentences on what the place is. Write it after the photos step. |
| `has` | no | Any of `fuel`, `food`, `stay`, `mechanic`, `atm`, `medical`, `network`. Only what research supports. |
| `wiki` | no | Exact Wikipedia article title, when it differs from the name. |
| `photo_query` | no | Search text for photos, when the name alone finds the wrong pictures. A list of strings merges candidates from several searches. |
| `photo_picks` | no | Candidate numbers from the latest contact sheet, best first, up to three. `[]` means no photo. If absent, the first candidate is used and the build warns you. |

## route_anchors (rarely needed)

A list of `{"lat": .., "lng": ..}` points the route must pass through, replacing the default of every stop. Use it only when a road has to be forced through a point that is not a stop.

## What the engine writes

In the trip folder: `build/route.json`, `build/places.json`, `build/photos.json`, `build/wiki.md`, `build/contact-N.png`, `build/thumbs/`, `build/check-overview.png`, `build/check-stop.png`, the final `<title>.html`, and a `.cache/` folder of downloaded data. All of it can be deleted and rebuilt.
