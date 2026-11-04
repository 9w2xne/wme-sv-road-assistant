# wme-sv-road-assistant
Manual-assistance userscript for WME — proposes draft segments over Street View coverage gaps. Never saves, never edits existing map data.

# WME Street View Road Assistant

A userscript for the Waze Map Editor (WME) that proposes **draft segments** over Street View coverage gaps. Every draft must be reviewed, aligned, and confirmed by the editor before it is saved.

**Status: Beta (1.0.0-beta).** Not yet audited by the Waze editing community. Use at your own risk. Nothing is saved without your explicit action.

---

## What this script does

- Scans the current map view for **Street View coverage gaps** — places where Street View imagery exists but no Waze segment is mapped.
- Traces those gaps into **candidate lines** shown on the map (orange dashed).
- On **Convert**, creates a real WME **draft segment** for each ticked candidate. The draft is editable like any other pending WME segment.
- Splits candidates at junctions (existing roads, skeleton forks) so you get one draft per logical piece.
- Warns about **seams** where an existing segment's flow orientation conflicts with the draft's — so you know which ends need manual attention before saving.

---

## What this script never does

- **Never saves.** Drafts stay pending in WME until you press WME's native **Save** button.
- **Never edits existing segments**, junctions, or turn restrictions.
- **Never connects drafts** to existing roads. You connect them by hand.
- **Never writes automatically** — every draft is created from a human click.
- **Never copies map data** from external sources. Street View coverage tiles are read as a reference-only image (see [Street View tile policy](#street-view-tile-policy)).

---

## How to use

1. Zoom to level 16 or higher.
2. Click **Scan** in the SV Roads panel.
3. Tick the candidates you want to convert.
4. Click **Convert**. Draft segments appear in WME.
5. Align each draft to the **real road centerline**.
6. Verify its shape against the **latest satellite imagery**.
7. Connect ends to existing roads by hand.
8. Press **WME's Save** when you're satisfied.

You can undo at any time with **Remove** in the panel or **Ctrl+Z** in WME.

### Candidate controls

| Control | Effect |
|---|---|
| Tick the checkbox | Selects the candidate for Convert or Remove |
| **⤶** button | Flip A↔B on the candidate (swaps which physical end is the from-node). Works before Convert only. |
| **2W / 1W** button | Toggle the candidate between two-way and one-way A→B. The converted draft inherits this. |
| **⤢** button | Zoom to the candidate |

### Seam column

The Candidates table has a **Seam** column showing what is at each end of the candidate line:

- **Blue dot** — that end is a junction (the trace was split here because of an existing road or a fork).
- **✓** — clean flow: the candidate's orientation matches the adjacent segment's expected direction.
- **⚠ old #123** — flow conflict with an existing segment that cannot be reversed by the script. You will need to fix this by hand in WME if it matters for one-way roads or turn restrictions.

---

## Known limitations (Beta)

- **Seam badges** in the Candidates table are computed at candidate creation time and are not re-stamped after a **flip**. The converted draft is unaffected; the badge is a pre-save hint only.
- **No GPS alignment.** Drafts are not snapped to GPS traces — you align them by hand in WME.
- **Junction orientation** comes from adjacent existing segments only. Where no adjacent segment exists, orientation follows the traced order.
- **Settings panel** collapses when a setting is changed. Reopen it to adjust another setting. This is a UI limitation scheduled for a later version.

---

## Street View tile policy

This script reads Street View **coverage tiles** from `mts*.google.com` as a read-only reference source. It checks whether a tile contains coverage (any alpha pixel) and uses that presence/absence as the input for candidate detection.

**No Google Maps content is copied into the Waze map.** The tiles are used only to identify *where coverage exists* — a binary "yes/no" per tile. The candidate geometries are traced from the shape of the coverage gap, not from the tile imagery.

If your local Waze community has a stricter policy about external reference sources, consult them before using this script.

---

## Requirements

- [Tampermonkey](https://www.tampermonkey.net/) (or a compatible userscript manager: Violentmonkey, Greasemonkey)
- Access to [Waze Map Editor](https://www.waze.com/editor)

---

## Install

1. Install Tampermonkey in your browser.
2. Open [`wme-sv-road-assistant-1.0.0-beta.user.js`](./wme-sv-road-assistant-1.0.0-beta.user.js) in this repository.
3. Click **Raw** to open the raw userscript URL.
4. Tampermonkey will prompt **"Install this script?"** — click **Install**.
5. Open WME. An **SV Roads** tab appears in the left sidebar.

### Updating

Tampermonkey checks the `@downloadURL` in the script header for updates. Because this script is not yet on Greasyfork, updates must be pulled manually — check this repository for newer versions.

---

## Settings

The Settings panel (collapsed at the bottom of the SV Roads tab) exposes:

| Setting | Default | What it does |
|---|---|---|
| Min map zoom to scan | 16 | Auto-zooms to this level before scanning |
| Zoom button level | 18 | Zoom level when you click the ⤢ button |
| Detection radius (m) | 20 | How far from existing roads the script treats a cell as "free" |
| Min candidate length (m) | 30 | Shorter traces are discarded |
| Max candidates stored | 1000 | Storage cap per session |
| Sub-batch size | 10 | Drafts per internal batch during Convert |
| Max drafts per click | 25 | Hard cap on how many drafts one Convert click can produce |
| WME save-warning threshold | 250 | Warns when pending edits approach this count (never blocks) |
| Auto-continue sub-batches | on | If on, one Convert click runs multiple sub-batches up to the per-click cap |
| Pause between batches (ms) | 2000 | WME settle time between sub-batches |
| Delay between segments (ms) | 300 | Per-segment delay |
| High-lock buffer (m) | 5 | Buffer around high-lock / protected segments |
| Road type | Street | The road type assigned to converted drafts |
| Split candidates at junctions | on | Splits traced candidates at existing-road crossings and skeleton forks |
| Auto scan resolution | on | Picks the scan zoom automatically based on viewport size |
| Tile budget per scan | 40 | Max coverage tiles fetched per scan |
| Auto show/hide coverage with Street View | on | Toggles the green coverage layer with the Street View panel |
| Responsibility acknowledgement | off | Must be checked once before Convert is enabled |

Settings are saved to `localStorage` under the key `wme-svra-settings`.

---

## About

Developed by **dinkaftac** (GitHub: [9w2xne](https://github.com/9w2xne)) with AI assistance (Claude, DeepSeek) for code review, safety analysis, and documentation drafting. All design decisions, safety boundaries (no auto-save, no edits to existing map data, no automatic connections), and the final code were reviewed and approved by the human author. The script was smoke-tested against the full workflow — Scan, Convert, Flip, Undo — before this Beta release.

The author takes responsibility for the script's behaviour and is the point of contact for the Waze guidelines review.

---

## License

MIT — see [LICENSE](./LICENSE).

---

## Disclaimer

This script is a review-and-approve tool, not an automated editor. It proposes drafts; **you** decide what to keep, correct, and save. Nothing is written to Waze's servers without your explicit click of WME's Save button. Follow your local Waze community's editing guidelines.rscript for the Waze Map Editor (WME) that proposes **draft segments** over Street View coverage gaps. Every draft must be reviewed, aligned, and confirmed by the editor before it is saved.

**Status: Beta (1.0.0-beta).** Not yet audited by the Waze editing community. Use at your own risk. Nothing is saved without your explicit action.

## What this script does

- Scans the current map view for **Street View coverage gaps** — places where Street View imagery exists but no Waze segment is mapped.
- Traces those gaps into **candidate lines** shown on the map (orange dashed).
- On **Convert**, creates a real WME **draft segment** for each ticked candidate. The draft is editable like any other pending WME segment.
- Splits candidates at junctions (existing roads, skeleton forks) so you get one draft per logical piece.
- Warns about **seams** where an existing segment's flow orientation conflicts with the draft's — so you know which ends need manual attention.

## What this script never does

- **Never saves.** Drafts stay pending until you press WME's native Save button.
- **Never edits existing segments**, junctions, or turn restrictions.
- **Never connects drafts** to existing roads. You connect them by hand.
- **Never writes automatically** — every draft is created from a human click.

## How to use

1. Zoom to level 16 or higher.
2. Click **Scan** in the SV Roads panel.
3. Tick the candidates you want to convert.
4. Click **Convert**. Draft segments appear in WME.
5. Align each draft to the **real road centerline**.
6. Verify its shape against the **latest satellite imagery**.
7. Connect ends to existing roads by hand.
8. Press **WME's Save** when you're satisfied.

You can undo at any time with **Remove** in the panel or **Ctrl+Z** in WME.

## Known limitations (Beta)

- **Seam badges** in the Candidates table are computed at candidate creation time and are not re-stamped after a **flip**. The converted draft is unaffected; the badge is a pre-save hint only.
- **No GPS alignment.** Drafts are not snapped to GPS traces — you align them by hand in WME.
- **Junction orientation** comes from adjacent existing segments only. Where no adjacent segment exists, orientation follows the traced order.
- **Street View tile sampling** reads `mts*.google.com` coverage tiles as a read-only reference source. No data is written back, and no Google Maps content is copied.

## Requirements

- [Tampermonkey](https://www.tampermonkey.net/) (or a compatible userscript manager)
- Access to [Waze Map Editor](https://www.waze.com/editor)

## Install

1. Install Tampermonkey in your browser.
2. Open `wme-sv-road-assistant-1.0.0-beta.user.js` from this repo.
3. Tampermonkey will prompt to install. Click **Install**.
4. Open WME. An **SV Roads** tab appears in the left sidebar.

## License

MIT — see [LICENSE](LICENSE).

## Disclaimer

This script is a review-and-approve tool, not an automated editor. It proposes drafts; **you** decide what to keep, correct, and save. Follow your local Waze community's editing guidelines.
