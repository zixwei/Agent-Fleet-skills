---
name: trip-map
description: Build an interactive 3D satellite trip map as a single HTML page from a start and an end point. It shows the real road route, hand-picked stops with photos, a day-by-day plan, an altitude profile, petrol pumps, places to eat and places to stay along the way, and a fly-through of the whole trip. Use this skill whenever someone wants to plan, map, visualise or share a road trip, bike ride, drive or any route between two places ("Delhi to Leh by bike", "map my drive from Noida to Gurugram", "where should I stop between A and B", "make me an itinerary I can share"), even when they don't say the word "map". Also use it for fuel-gap or night-halt planning along a route.
---

# Trip map

You turn "I'm going from A to B" into one HTML file the person can open, click through and share. The page has a satellite map with 3D terrain, a list of stops with photos, a card for each stop, an altitude strip, browsers for fuel, food and stays, and a Play Trip fly-through.

The work is split in two, and the split matters:

- **The engine** (`scripts/tripmap.py` and `assets/template.html`) does everything mechanical: finds the road, measures distance and altitude, collects pumps, hotels and restaurants from OpenStreetMap, fetches photo candidates, and builds the page. Do not rewrite or hand-edit it for one trip. It is tested; fresh map code written per trip is not.
- **You** supply the judgement, in one file called `trip.json`: which stops are worth making, how to split the days, what to warn about, which photos are good, and short honest text. This is the part a router cannot do, and it is what makes the map worth opening.

Needs Python 3.9+ and internet access. A Chrome-family browser is optional; with it, the engine makes contact sheets and a screenshot for you to look at.

## Workflow

Work in a new folder for the trip (for example `./noida-gurugram/`). The engine keeps its cache and build files there.

### 1. Pin down the trip

You need a start and an end. Ask only for what is missing and changes the plan. Sensible defaults when unstated: a car, the usual road, and as many days as the distance suggests (plains 250–350 km a day, mountains 80–250 km).

When the person names only a city ("Noida", "Leh"), start or finish at a well-known central point there, such as the main market or a landmark with parking. Say which point you chose in your final message and offer to rebuild from their real address.

### 2. Research before writing anything

Search the web for how people actually travel this route. You are looking for:

- the road most travellers take, and any alternative the router might pick instead;
- where people stop and why (food, views, towns, fuel, night halts);
- anything that can ruin the trip: long stretches without fuel, seasonal closures, permits, altitude, bad sections, traffic windows.

Keep a note of what came from where. If you have no web access, say so in your final message and build from what you know, marked as unverified.

### 3. Write `trip.json`

Read `references/trip-format.md` for every field. `examples/delhi-leh.json` is a full worked trip. The short version:

- `meta`: title, a one-line label, the verb for the mode (`driving`, `riding`), region and country to help place lookups, and a one-sentence summary.
- `days`: one entry per travel day with a realistic average pace.
- `stops`: in travel order, first one `start`, last one `finish`. Each has a type, the day it belongs to, why to stop, one practical tip, and what is available there.

Pick stops a person would thank you for: where to eat, where the view is, where to fill up before a long gap, where to sleep. Five to eight stops, counting the start and the finish, suits a day trip; a multi-day trip may have twenty or more. Leave places out rather than pad the list.

For locations, give a stop a short `q` in the form "name, city" ("Humayun's Tomb, Delhi") and let the engine look it up. Longer queries with neighbourhoods or districts tend to find nothing. Only supply `lat` and `lng` yourself when you are sure of them, because a remembered coordinate that is slightly off puts the pin on the wrong road.

The route is drawn through every stop in order, because a stop is a place the person drives to. For something they only see from the road, such as a monastery on a hill, add `"via": false` and the pin is placed at the nearest point of the route instead.

If one warning applies to the whole trip (a vehicle restriction, a permit, a seasonal closure), put it in `meta.notice`. It is shown on the title screen and above the stop list, where a tip on one card would be missed.

### 4. Build the route and read the table

```bash
python3 <skill>/scripts/tripmap.py route trip.json
```

This prints one line per stop: distance from the start, altitude, how far the stop is from the routed road, and what the lookup matched. Read it like a reviewer:

- **"found as" is the place the lookup matched.** Check every line. Another town of the same name, or a lake's far shore, needs a better `q` or coordinates. A note such as `[searched "..."]` means the engine had to shorten your query to find anything, so look twice.
- **Total distance that disagrees with your research** usually means the router chose a different road between two stops. This happens: on Delhi to Leh the router quietly took a new road through Zanskar instead of the classic highway. Add a stop, or a `route_anchors` point, on the road you intend.
- **A total a few km longer than expected** in a city can come from a stop pinned on the far side of a divided road, which adds a loop. It is harmless; move the stop's point if it bothers you.
- **A stop flagged far from the road or out of order** is either in the wrong place or not reachable by car there.
- **Pass altitudes** deserve a second look. `"refine": "peak"` moves a stop to the high point of the road.

### 5. Collect places

```bash
python3 <skill>/scripts/tripmap.py places trip.json
```

This asks public OpenStreetMap servers for pumps, food, stays, ATMs and medical help near the stops and along the road. These servers are free and sometimes overloaded, so the step can take minutes and may report parts as MISSING. The page still builds without them; run the step again later to fill the gaps. It prints the longest stretches between petrol pumps. Compare that with your research, and say so in a stop's tip when a long gap is real.

### 6. Choose photos by looking at them

```bash
python3 <skill>/scripts/tripmap.py photos trip.json
```

This writes `build/wiki.md` (short Wikipedia notes per stop) and contact sheets `build/contact-N.png` with numbered photo candidates for each stop. Open the contact sheets and look. The search returns plenty of wrong pictures (food, signboards, strangers, a different place with the same name), and one bad photo makes the whole page look careless. For each stop set `"photo_picks": [numbers]`, best first, up to three, preferring wide shots of the place itself. Use `[]` when nothing fits. To judge a candidate more closely, open `build/thumbs/<stop>-<number>.jpg`.

If a stop has no good candidates, set a different `"photo_query"` and run the step again. The numbers belong to the candidate list of the latest run, so after changing a query, look again and re-pick; the engine tells you which stops changed. To draw candidates from more than one search, such as a stop that covers two places, give `photo_query` a list of queries.

`wiki` is only needed when the Wikipedia article title differs from the stop name. Many places have no article; leave the field out and move on.

While you are here, write each stop's `"about"`: one or two plain sentences on what the place is, using `wiki.md` and your research. Write it yourself. Pasted encyclopedia openers read badly on a card.

### 7. Build, check, hand over

```bash
python3 <skill>/scripts/tripmap.py build trip.json
python3 <skill>/scripts/tripmap.py check trip.json
```

`build` writes the page next to `trip.json` and lists anything unfinished (unreviewed photos, missing about lines, missing place data). `check` loads the page in a hidden browser and saves `build/check-overview.png` and `build/check-stop.png`. Look at both: the first should show the map, route and stop list, the second a stop close up with its photo card. It also reports any errors the page raised. Then open the page for the person (`open file.html` on macOS) and tell them, briefly:

- what the trip looks like (distance, days, number of stops);
- which facts you checked and where, and which parts are suggestions;
- anything missing or uncertain, such as place data that did not load.

The steps cache their results, so editing `trip.json` and running a step again is cheap. If you add, remove, rename or reorder stops, run `route` again before `build`.

## Writing for the page

The text sits on small cards over a map, so keep it short and concrete.

- **why**: the reason this stop earns its place. "Last fuel before a 320 km gap" beats "A popular stop".
- **tip**: one practical thing a first-timer would not know.
- **has**: only what your research supports. The page shows these as facts.
- **pace**: honest averages including traffic and breaks. Rough guide in km/h: city 20–25, plains highway 50–60, hills 30–40, high mountain roads 25–30.
- **summary**: one sentence for the title screen. You can use `{km}`, `{days}`, `{stops}`, `{pumps}`, `{gap}` and `{high}`; the page fills in measured values, which keeps the sentence true if the route changes.

People may ride on what this page says. Do not invent pumps, opening hours or road conditions. When something is reported rather than confirmed, write it that way. Anything safety-related (altitude, fuel range, closures) belongs in a tip, stated plainly.

## Look and theme

The default look is black panels, thin lines and uppercase type over darkened satellite imagery. Set `meta.theme.accent` to a hex colour to change the route and highlight colour; if the person has a `DESIGN.md`, take its accent from there. Deeper restyling means editing the `:root` variables at the top of `assets/template.html`, which changes every future trip, so only do that when asked.

## When something fails

- **No route returned**: the routing server is a free demo and occasionally busy. Wait a minute and retry. If one stop is on an island or in a pedestrian zone, move it to the nearest road.
- **Places MISSING**: run `places` again later. Tell the person what is missing rather than hiding it.
- **Photos time out or come back thin**: the photo service limits request speed, and the engine already goes slowly. Run the step again; finished stops are cached.
- **`check` says the page did not finish loading**: run it again; the first load downloads map imagery and can be slow. If it reports page errors, the trip data is the likely cause, so re-read the last thing you changed in `trip.json`.
