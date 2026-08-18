# USMockUp

Exploratory work for a possible "United States View" on the Global Crisis
Dashboard — a district-by-district hazard reference for EFCA's 17 US
districts, alongside the existing global view. This repo is intentionally
separate from the two production repos (the private app repo and the public
`crisis-dashboard-data` repo) while this stays in the concept/mockup stage —
nothing here is wired into the live app yet.

## Status at a glance

| Piece | Status |
|---|---|
| District hazard profiles (18 FEMA hazard types × 17 districts) | **Real, verified data** |
| County-to-district territory map (13 multi-district states) | **Real, approximate** — built from actual church locations, not an official EFCA boundary |
| Mockup UI (`us_view_mockup.html`) | Working, uses the real data above |
| Historical trend charts (1970–present) | Not built — current files are a risk *snapshot*, not a time series |
| "Looking forward" outlook | Hand-written for 3 of 17 districts (Southeast, West, Midwest); a data-grounded one-liner for the other 14 |
| "Notable major disasters" list | Hand-picked, real events for the same 3 districts; placeholder for the other 14 |
| Community trauma events (school shootings, etc.) | Not connected anywhere — deliberately shows only the intended source, no content |

## Files in this repo

**`us_view_mockup.html`** — the mockup itself. Self-contained, single-file,
opens in any browser. Shows all 17 EFCA districts; pick one from the chip row
at the top. Real FEMA hazard data drives the "Key hazard profile" and "Hazard
severity, by type" sections for every district. A banner at the top of the
page states plainly what's real vs. illustrative — that banner is the source
of truth if anything below goes stale.

**`us_view_hazard_profiles.json`** — the real hazard data. One entry per
district: county count, population, an overall FEMA Risk Index score, and a
score (0–100, a national percentile) for each of FEMA's 18 hazard types
(hurricane, wildfire, tornado, earthquake, drought, flooding, and so on).
Pulled directly from FEMA's National Risk Index (the public Esri ArcGIS
endpoint it's built on). The file's own `methodology` and `caveats` fields
explain exactly how each number was computed and what its limits are — read
those before reusing the numbers elsewhere.

**`us_view_district_crosswalk.json`** — the county-level territory map that
produced the file above. For each district: which states it owns entirely,
and for the 13 states split between multiple districts, the specific county
list assigned to each one. Includes a `known_gaps` section listing the three
district-fragments (parts of Allegheny, Inland Mountain, and Rocky Mountain)
whose exact county lists weren't preserved separately — their hazard numbers
are still real and correct, just not independently re-derivable from this
file alone without a fresh fetch.

**`district_boundary_crosswalk.md`** — the human-readable writeup behind the
crosswalk JSON. State-by-state detail on how each split was determined (real
church addresses pulled from each district's own "Find a Church" page),
confidence levels (HIGH/MEDIUM/LOW depending on whether an official directory
existed), and the handful of genuine ambiguities found along the way — most
notably a single Louisville, KY church claimed by two different districts'
own materials, and a contested town in northwest Minnesota. Worth a real
answer from EFCA directly if county-level precision ever matters more than it
does for a hazard overview.

**`US_VIEW_DATA_FEASIBILITY.md`** — written earlier, before the hazard data
and crosswalk were actually pulled. Its sections on historical trend data
(NOAA Storm Events, HURDAT2, NIFC wildfire acreage), the forecast/outlook
framing, and the community trauma section are still accurate and are the best
starting point for building those out next. Its opening section describing
the district-to-county crosswalk as future work, and the hazard profile as
still-illustrative, is now out of date — both are done, per the two JSON
files above.

## Methodology, in brief

Every EFCA district was mapped to a set of US counties: whole-state where a
district owns a state outright, and a specific county list where a state is
split between multiple districts (13 states are split this way). Splits were
determined empirically — by pulling real church addresses from each
district's own "Find a Church" directory and seeing where they actually
land — since EFCA's own district descriptions are directional ("eastern
Ohio," "western Pennsylvania") rather than a published county line. FEMA
National Risk Index scores were then pulled for every county in that map and
averaged (unweighted, per county) up to the district level.

This produces a genuinely useful approximation for the stated purpose —
helping districts anticipate the hazards they typically face — but it is not
an official EFCA boundary and shouldn't be presented as one. A handful of
counties in sparsely-churched areas (central South Dakota, the middle of
Michigan's Upper Peninsula, rural central Nevada) have no real evidence
either way and were left unassigned rather than guessed at; see
`district_boundary_crosswalk.md` for the full list of these honest gaps.

## Suggested next steps

In roughly the order they'd add the most value:

1. Pull real historical event data (NOAA Storm Events Database and/or
   OpenFEMA Disaster Declarations) to replace the current risk-index
   *snapshot* with an actual 1970–present trend chart per district — this is
   the biggest remaining gap between the mockup and the original ask.
2. Do the same curated "notable major disasters" research for the other 14
   districts that Southeast, West, and Midwest already have.
3. Decide on the trauma-events section as a ministry question, not just a
   data one, before connecting the K-12 School Shooting Database — see
   `US_VIEW_DATA_FEASIBILITY.md`'s note on this.
4. Once the concept is approved, scope how it plugs into the real PWA
   (new tab vs. a top-level view toggle like the mockup's, whether it reuses
   the existing `renderChart` canvas engine or the mockup's inline-SVG
   approach) and where these data files' *permanent* home should be — likely
   alongside `timeseries.json`/`kpis.json` in the existing two-repo setup,
   not this standalone mockup repo.
