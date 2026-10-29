# nearby-places-data

Ready-made regional data files for the **Muflihin Pro** app feature "Mosques & Halal Nearby".
The app downloads only the small regional files around the user, so no location is sent to any server
and no slow public map query is needed.

- `data/v1/index.json` — list of tiles (bounding box, element count, file name, size), snapshot date.
- `data/v1/mosque/<tile>.<sha8>.json.gz` — mosques and prayer rooms (`religion=muslim` + `amenity=place_of_worship`, or `building=mosque`).
- `data/v1/halal/<tile>.<sha8>.json.gz` — restaurants, fast-food places and cafés with `diet:halal=yes|only|limited|some`.

File names contain a content hash, so a file never changes after publication.
Each tile is gzip-compressed JSON: `{"v":1,"id":"L1x38y28","b":[south,west,north,east],"g":"<build date>","e":[[type,id,lat,lon,{tags}],…]}`
where `type` is `n`/`w`/`r` (node/way/relation) and `tags` is a whitelist of OpenStreetMap tags
(names in the app languages, address, phone, website, opening hours, `denomination`, `diet:halal`, `check_date`, …).
Tiles form a quadtree starting from 10°×10° cells, split until a tile holds at most ~1500 places; an element belongs to the tile that contains its centre.

Nothing in these files is inferred: a mosque's denomination or a restaurant's halal status is exactly what OpenStreetMap contributors recorded.
Missing information stays missing.

## Source and licence

Data © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright), made available under the
[Open Database License (ODbL) 1.0](https://opendatacommons.org/licenses/odbl/1-0/).
This repository is a derived database of OpenStreetMap and is therefore also offered under the ODbL; any use must keep the attribution
"© OpenStreetMap contributors" and share derived databases under the same licence. See `LICENSE.md`.

Extracted from the QLever OSM planet index (<https://qlever.dev>) on the planet dump date recorded in `index.json` (`osmTimestamp`).
The OpenStreetMap data may be incomplete or out of date: please correct mistakes directly on <https://www.openstreetmap.org>.

## Updating

The generator and checker live with the app source (`scripts/nearby-data/build.mjs`, `verify.mjs`). A rebuild bumps `revision` in `index.json`
and replaces the tile files; the app picks up the new index automatically (cached for 6 hours).
