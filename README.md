# commute-map

An interactive map of how long people spend getting to work in US metro areas.

Each of the 384 metropolitan statistical areas (MSAs) in the 2019 American Community Survey appears as a circle. Circle **area** shows the metro's 2019 population, and circle **color** shows the mean one-way commute time. Hover over a circle to see the metro's commute time, population and public-transit share.

The national mean in 2019 was **27.6 minutes**.

## What the map shows

- **Longest commutes:** East Stroudsburg, PA (39.7 min), New York, NY (37.7), Stockton, CA (36.3)
- **Shortest commutes:** Walla Walla, WA (14.5 min), Great Falls, MT (16.5), Lubbock, TX (16.8)
- Among large metros, New York, Washington and San Francisco have the longest commutes. They also have the highest transit shares.

Under the map, a table lists the 15 largest metros with their population, commute time and transit share.

## Running it

The whole project is one file, `index.html`, and there is no build step.

```bash
git clone https://github.com/mienaiaosora/commute-map.git
cd commute-map
open index.html        # macOS; on Linux use xdg-open, on Windows use start
```

You can also serve the folder locally, for example with `python3 -m http.server`, and open `http://localhost:8000`.

You need an internet connection because the page loads its libraries and the state outlines from CDNs:

| Dependency | Version | Source |
|---|---|---|
| [D3](https://d3js.org/) | 7.8.5 | cdnjs |
| [TopoJSON client](https://github.com/topojson/topojson-client) | 3.0.2 | cdnjs |
| [us-atlas](https://github.com/topojson/us-atlas) (`states-10m.json`) | 3.x | jsDelivr |

To host the map, enable **GitHub Pages** for the `main` branch in the repo settings, and GitHub will serve `index.html` at the site URL.

## Data

| Field | Source |
|---|---|
| Mean travel time to work | US Census Bureau, ACS 2019 1-year, geographic comparison table **R0801** |
| Share of workers using public transit | ACS 2019 1-year, table **R0804** |
| Population | Census Bureau population estimates, Vintage 2019 |
| Metro coordinates | 2019 CBSA Gazetteer file (internal points) |

Some notes on the data:

- Commute times are for workers aged 16 and over who **did not work at home**.
- The ACS 1-year release only covers areas with roughly 65,000 or more people, so smaller metros are not on the map.
- The data is from 2019, before the pandemic changed commuting and remote work.
- One metro has no transit-share value, and its tooltip leaves that line out.

The data is embedded in `index.html` as a `DATA` array. Each row has this form:

```js
["Metro name, ST", population, commute_minutes, transit_share_pct, latitude, longitude]
```

## How it works

- **Projection:** `d3.geoAlbersUsa`, which places Alaska and Hawaii in insets.
- **Circle size:** a square-root scale (`d3.scaleSqrt`), so circle area is proportional to population. Small metros get a minimum radius so they stay visible.
- **Color:** a threshold scale with five bins: under 21, 21–24, 24–27, 27–30, and 30 or more minutes.
- **Labels:** the ten largest metros are labeled with their name and rounded commute time.
- **Dark mode:** the page follows the system `prefers-color-scheme` setting. You can force a theme by setting `data-theme="light"` or `data-theme="dark"` on the `<html>` element.

## Customizing

All of these settings are near the top of the `<script>` block in `index.html`:

- **Change the color bins:** edit `bins`, `labels`, and the `light` and `darkc` palettes. Each palette needs one more color than `bins` has entries.
- **Change which metros are labeled:** edit the `lab` array. Names must match the `DATA` names exactly.
- **Change circle sizes:** adjust the range in `d3.scaleSqrt([0, 2e7], [0, 26])`. The second array is the radius in pixels at 0 and at 20 million people.
- **Use newer data:** replace the rows in `DATA` with values from a later ACS release, then update the year, the US mean and the source notes in the page text.

## License

This repository does not have a license yet. The underlying Census data is public domain.
