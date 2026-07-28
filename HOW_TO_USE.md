# County Scanner — How To

An ADIF log scanner for CQ USA-CA county awards. It reads your log, resolves POTA, WWFF, and SOTA references to counties, compares the result against a needed-counties list, and tells you what's left.

Everything runs in your browser. No data is uploaded anywhere. Open `adif_county_scanner.html` by double-clicking it, or host it on a website — it's a single self-contained file.

> **Before applying for any award, manually verify park locations.** Reference-to-county mappings come from community-maintained tables and can be out of date.

---

## Quick start

1. Open the HTML file in a browser.
2. **Panel 01** — select your ADIF log.
3. **Panel 02** — select your park lookup (the KFF Reference List `.xls` is the best source) and SOTA lookup.
4. **Panel 03** — leave "Full USA-CA county list" selected, or add an award file.
5. **Panel 04** — pick a band and mode if you're chasing a specific award.
6. Read the **Needed** tab.

The four-number display at the top always reflects your current filters: QSOs scanned, county credits, counties worked, counties still needed.

---

## Panel 01 — Log file

Select one or more `.adi` / `.adif` files. Multiple logs are merged.

The scanner reads these ADIF fields:

| Purpose | Fields |
|---|---|
| Park/summit references | `POTA_REF`, `SOTA_REF`, `WWFF_REF`, `SIG` + `SIG_INFO` |
| Direct county | `CNTY` (both `Cook` and `IL,Cook` forms), `STATE` |
| Filtering | `BAND`, `MODE`, `SUBMODE`, `QSO_DATE` |
| Satellite | `PROP_MODE=SAT`, `SAT_NAME`, `SAT_MODE` |
| Comment scanning | `COMMENT`, `NOTES` |

WWFF logs typically use `SIG=WWFF` with `SIG_INFO=KFF-1234`; that's handled. Unlabeled `SIG_INFO` values are identified by their shape.

Warnings appear directly under the file name — legacy `K-` references found, satellite QSOs detected.

---

## Panel 02 — Lookup tables

### Park lookup (required for POTA/WWFF credit)

Accepts either format:

- **KFF Reference List (`.xls`)** — recommended. The WWFF/POTA park-county workbook. All sheets are parsed, including "Multi-State & Combined", giving roughly 6,800 POTA and 7,300 WWFF references plus park names. Requires a brief internet connection the first time (it loads an Excel reader from a CDN). If you're offline, save the data sheet as CSV instead.
- **TSV/CSV** — the older `park_lookup.csv` format (WWFF-ID, POTA-ID, name, type, `US-XX`, counties). UTF-16 and tab delimiters are auto-detected.

### SOTA lookup (required for SOTA credit)

`sota_lookup.csv` — SOTA-REF, county, state.

### POTA hunter_parks export (optional)

Your "Parks Hunted" CSV from pota.app. Feeds the **Park matches** tab.

### POTA all-references list (optional)

The pota.app entity export, e.g. `United States of America (US).csv`. It has no county data, but it puts **park names** on references that aren't county-mapped yet, so you can research them yourself. Shows up in the Issues tab.

**Website deployment:** put `park_lookup.csv` and `sota_lookup.csv` in the same folder as the HTML file and they load automatically — visitors only upload their log.

---

## Panel 03 — Needed counties

### Full USA-CA county list

All 3,077 counties. Use this when starting from zero on a band or award. The dropdown beneath it sets the award type:

- USA-CA (any call)
- K / W / N / A Prefix Award
- All Prefix Award
- Call Combo 1x2, 2x1, 1x3, 2x2, 2x3

### Award files

"Add award file(s)" accepts **multiple `.txt` files at once** — each becomes a selectable entry, so you can flip between awards without reloading. The award type is read from the **file name**:

| File name contains | Recognized as |
|---|---|
| `USA-CA` | Plain county award |
| `USA-PA` | All Prefix Award (K+W+N+A per county) |
| `USA-PA - K` / `- W` / `- N` / `- A` | Single prefix award |
| `Call Combo - 1x2` (or 2x1, 1x3, 2x2, 2x3) | Call sign combo award |
| `1.25 Meters Award`, `6 Meters Award`, `70 Centimeters Award` | Band award — **locks the band filter** |
| `CW Award`, `SSB Award`, `Digital Award` | Mode award — **locks the mode filter** |
| `7th Time Award` (any ordinal) | Repeat award — prompts you to set a start date |

File contents must be the CQ record-book format: a `State:` line followed by comma-separated counties.

**Unrecognized file names are rejected with an explanation** rather than being loaded incorrectly. Dismiss the error with its ✕ button.

Each loaded award has an **✕** button to remove it. Removing the selected award falls back sensibly; removing the last one returns to the full list.

### USA-CA Satellite

If your log contains satellite QSOs, a 🛰 **USA-CA Satellite** entry appears automatically. Satellite QSOs are **excluded from every other award** and counted only here, so they can never inflate your terrestrial totals.

---

## Panel 04 — Filters

- **Mode** — All / SSB-Phone / CW / Digital. SSB, USB, LSB, AM, FM and digital voice group as phone; CW is CW; everything else (FT8, FT4, RTTY, PSK…) is digital.
- **Band** — populated from the bands actually present in your log.
- **Date range** — restricts which QSOs count. **Essential for repeat awards:** set the start date to when you began this round (the day after your previous award). QSOs without a date are excluded whenever a range is active.
- **Credit sources** — turn POTA, WWFF, SOTA, or the CNTY field on and off independently.
- **Credit multi-county parks** — see the Worked tab section below.
- **Include MY_\* references** — count your own activations.
- **Scan comments for park/summit refs** — picks up references left in COMMENT/NOTES, e.g. `K-2121`. On by default; credits are marked 💬.

When a band or mode award is selected, the corresponding control locks and displays 🔒 with the reason. Deselect the award to unlock.

---

## The tabs

### Needed

Counties you still need, grouped by state with a progress bar. Search, collapse/expand, and export as `.txt` (same format as the CQ file) or `.csv`.

Under the All Prefix Award, partially-worked counties show which prefixes are missing, e.g. `Cook need K,A`.

### Worked

Counties with credit under the current filters, showing how each was earned and the first qualifying QSO. Callsigns link to QRZ.com.

Markers:

| Mark | Meaning |
|---|---|
| ⚠ | Credit came from a multi-county park — county not confirmed |
| ⚑ | A-prefix wildcard used (DX or a call containing X) |
| ≠ | The log's state disagrees with the lookup table |
| K→US | Old-style POTA reference, auto-translated |
| 💬 | Reference was found in a comment field |

Checkboxes select which counties are included in exports; **Select all** / **Select none** are provided.

- **Export credited QSOs (.adi)** — the QSOs that earned your selected counties, as a valid ADIF file.
- **Download .csv** — the same list as a spreadsheet.

**Multi-county parks are never exported.** A park spanning several counties can't prove which one the activator was in, so those credits are display-only. They're listed separately in Issues.

### QSO credits

Every individual QSO that produced county credit, with date, call, band, mode, reference, and resolved county. Alaska entries show the original borough in parentheses.

### Park matches

Needed counties that contain a park you've **already hunted** (from the hunter_parks file). Activators return to the same parks, so these are your most realistic targets. Shows park reference, name, your QSO count, and first-hunted date.

### Issues

Everything that needs your attention. Each section has its own CSV export.

1. **County names found in comments** — county-line and mobile notes like "Also Unicoi". See the workflow below.
2. **Multi-county park credits** — worked references spanning several counties, with all counties listed and QRZ links to the activators. Contact them to pin down the county or arrange a sked.
3. **POTA reference / state conflicts** — the log and the lookup disagree about the state. POTA occasionally reassigns numbers; verify which is correct.
4. **References not found** — unknown references. Rows marked ◆ carry county data in the QSO itself, a strong hint the number was reassigned. Load the POTA all-references list to see park names here.
5. **Legacy POTA references** — old `K-` numbering. Credited automatically, but update your log; a fix list CSV is provided.
6. **Counties not on the master list** — usually Virginia independent cities, DC, or spelling differences.

---

## Workflow: claiming counties mentioned in comments

County-line and mobile contacts often note the second county in the comment. The scanner finds these but **never credits them automatically** — you decide.

For each flagged mention you get:

- The QSO's existing **state and county**, so you can see what the second county pairs with
- A **state selector** (candidates from the QSO's state first)
- A **dropdown of that state's valid counties**
- Two actions:
  - **Add duplicate QSO** — keeps the original and adds a copy carrying the second county. This is the county-line case. References are stripped from the copy since the county came from the comment, and it's tagged `APP_COUNTYSCANNER_SOURCE`.
  - **Set county** — overwrites the county on the original QSO. Use this when the logged county was wrong or abbreviated.

Both validate against the master list; an invalid name is rejected inline.

> **A single QSO can be claimed for at most two counties.** Additional duplicates won't be accepted by the award sponsor.

Optional: **Clear the COMMENT/NOTES field on QSOs I change** tidies the log once the county has moved into `CNTY`.

Then export:

- **Export modified log (.adi)** — your whole log including all additions and edits.
- **Export changed QSOs only (.adi)** — just the added and edited QSOs, for importing corrections without re-importing everything.

---

## Name matching

County names are compared with punctuation and spacing ignored, so these all match:

- `St. Clair` = `St Clair` = `Saint Clair`
- `Ste. Genevieve` = `Sainte Genevieve`
- `Third J.D. SC` = `Third J. D. S C`

**Alaska** boroughs and census areas are mapped to the four USA-CA judicial districts, including renamed and retired names:

| District | Includes |
|---|---|
| First J.D. SE | Haines, Hoonah-Angoon, Juneau, Ketchikan Gateway, Petersburg, Prince of Wales-Hyder, Sitka, Skagway, Wrangell, Yakutat (and the old combined areas) |
| Second J.D. NW | Nome, North Slope, Northwest Arctic |
| Third J.D. SC | Aleutians East/West, Anchorage, Bristol Bay, Chugach (Valdez-Cordova), Copper River, Dillingham, Kenai Peninsula, Kodiak Island, Lake and Peninsula, Matanuska-Susitna |
| Fourth J.D. C | Bethel, Denali, Fairbanks North Star, Kusilvak (formerly Wade Hampton), Southeast Fairbanks, Yukon-Koyukuk |

---

## Prefix and combo award rules

- **K / W / N awards** — first letter of the callsign must match.
- **A award** — direct A-prefix US calls count. Wildcards: a DX callsign, or any call containing the letter X. A wildcard counts for only **one** prefix per county; the scanner won't spend a county's only K/W/N contact as its A wildcard.
- **All Prefix Award** — a county counts only when all four of K, W, N, and A are covered there. Partial counties stay in the Needed tab showing what's missing.
- **Combo awards** — US calls only, matched on prefix×suffix structure (1x2, 2x1, 1x3, 2x2, 2x3). Portable suffixes like `/7` are ignored when determining structure.

**DX handling for comment scanning:** stations outside the US are ignored, except when operating portable in the US (`W9/MM9SQL` or `MM9SQL/W9`). A US call operating abroad (`NJ7H/VE3`) is correctly excluded, as is maritime mobile (`/MM`).

---

## Troubleshooting

**"No county credit from my POTA QSOs"** — load the park lookup in Panel 02. Without it, references can't be resolved.

**"The .xls won't load"** — the Excel reader loads from a CDN and needs internet access on first use. Offline: open the workbook, save the data sheet as CSV, and upload that.

**"Lots of unmatched references"** — the lookup tables only cover parks in the WWFF/POTA cross-reference. Newer parks aren't there. Load the POTA all-references list to at least see their names.

**"My repeat award count looks too high"** — set the start date in Panel 04.

**"A county I worked isn't credited"** — check that its credit source is enabled in Panel 04, that the band/mode filters aren't excluding it, and look in Issues for an unmatched reference or a state conflict.

**"Satellite QSOs are missing from my award"** — that's intentional. They're tracked separately under USA-CA Satellite.
