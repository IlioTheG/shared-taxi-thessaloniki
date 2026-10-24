# Shared taxi rides in Thessaloniki

An interactive dashboard of shared taxi trips in Thessaloniki, Greece, over one day of ride requests. Choose a time window and the map shows which requests were combined into shared trips, together with all requests of that window.

The shared trips were pre-computed for my diploma thesis in Spatial Planning and Development at the Aristotle University of Thessaloniki, *Shared-mobility services and shared-taxi optimization. The case study of "Beat" taxi in Thessaloniki* (2021). Each shared trip combines at most two ride requests. This repository is for demonstration only and does not contain the pairing algorithm.

## Live dashboard

`index.html` is a standalone dashboard that runs in any browser. Choose a time window on the timeline or press **Play day** to move through the day. Click a shared trip to see its two requests and how much distance sharing saved.

Each shared trip is an animated route in its own colour. Solid circles are pick-ups and hollow circles are drop-offs. Green lines show all requests of the selected window. The layers can be switched on and off in the side panel.

To publish it with GitHub Pages, go to **Settings → Pages**, set the source to **Deploy from a branch**, choose `main` and `/ (root)`, and save. The dashboard then appears at `https://<username>.github.io/<repository>/`.

The page loads its data with `fetch`, so it does not work when opened directly from disk. To test it locally, run `python -m http.server` in this folder and open `http://localhost:8000`.

## Contents

| File | Description |
| --- | --- |
| `index.html` | The dashboard |
| `data/requests.json` | 2,557 ride requests (solo trips) |
| `data/shared_trips.json` | 793 shared trips, each serving two requests |
| `lib/leaflet/` | Leaflet 1.9.4 (BSD 2-Clause license) |
| `lib/leaflet-ant-path/` | Leaflet AntPath 1.3.0 for the animated routes (MIT license) |

## Data

Both files contain a list of `fields` and a list of `rows`, with one row per request or shared trip. Coordinates are `[longitude, latitude]` in WGS 84. The `window` value is the 5-minute time window, counted from 08:00 (0 is 08:00 to 08:05 and 179 is 22:55 to 23:00).

**requests.json**

| Field | Description |
| --- | --- |
| `id` | Request ID (R0001 to R2557) |
| `window` | 5-minute window in which the request was made |
| `origin`, `destination` | Pick-up and drop-off point |
| `time_s`, `length_m` | Travel time (seconds) and length (metres) of the solo route |
| `path` | Solo route as a list of coordinates |

**shared_trips.json**

| Field | Description |
| --- | --- |
| `id` | Shared trip ID (S001 to S793) |
| `window` | 5-minute dispatch window |
| `method` | Stop order. FIFO picks up 1, picks up 2, drops off 1, drops off 2. FILO picks up 1, picks up 2, drops off 2, drops off 1 |
| `request_1`, `request_2` | The two requests served, matching `id` in the requests file |
| `origin_1`, `destination_1`, `origin_2`, `destination_2` | Pick-up and drop-off points of both requests |
| `time_s`, `length_m` | Travel time and length of the shared route |
| `path` | Shared route as a list of coordinates |

## Anonymization

The data has been anonymized before publication.

* All original user and request IDs were removed and replaced by new sequential IDs.
* Every pick-up and drop-off point was moved once in a random direction and snapped to the nearest node of the road network. Each point lies at least 150 m and usually no more than 500 m from its real location. The same moved point is used in both files, so solo trips and shared trips still correspond.
* All routes were recalculated on the road network (fastest path by travel time) from the moved points. Travel times and lengths therefore differ from the original results.
* Only the 5-minute time window of each request is kept, without the date.

The routes and figures are therefore suitable for demonstration and not for further analysis.

## Credits

Road network from OpenStreetMap contributors. Basemap tiles by Esri. Map library [Leaflet](https://leafletjs.com).
