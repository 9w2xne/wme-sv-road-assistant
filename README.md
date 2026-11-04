# WME Street View Road Assistant

A manual-assistance userscript for the Waze Map Editor (WME). It proposes **draft** road segments over places where Street View coverage exists but no Waze segment is mapped. You review every draft, align it to the real road, and save it yourself.

> **Status: 1.0.0 beta.** Not audited by the Waze editing community. Use at your own risk.

## What it does

- Scans the current map view for Street View coverage that has no mapped Waze segment nearby.
- Traces candidate lines from that coverage and lists them in a table.
- Splits candidates at existing junctions and flags orientation "seams" where a draft meets a mapped road.
- On **Convert** (a click from you), creates the ticked candidates as new draft segments in WME.

## What it never does

- It never saves. Drafts stay pending until you press WME's own **Save** button.
- It never edits, deletes or moves existing segments, junctions or turn restrictions.
- It never connects drafts to existing roads. You connect them by hand.
- It never copies Google Maps content into the Waze map. See [Street View tile policy](#street-view-tile-policy) below.

## How candidate shapes are made (please read)

The script requests Street View coverage tiles from `mts*.google.com` and **derives candidate line shapes from the coverage pixels**. The shapes are only a starting guess, and they can be offset, cut short, or wrong.

- Treat every draft as unverified. Align it to the real road centerline using satellite imagery or other sources you are allowed to use, then verify it before saving.
- Do not rely on the draft geometry as it is.
- The tile endpoint is unofficial and may change or stop working without notice.
- Check your local Waze community's rules on external reference sources before using or sharing this script.

## Street View tile policy

The script reads Street View **coverage tiles** only to determine **where coverage exists** (a binary yes/no per tile, based on pixel alpha presence). No Google Maps content — no road geometry, no imagery, no labels — is copied into the Waze map. The candidate geometries are traced from the *shape of the coverage gap*, not from the tile imagery itself.

If your local Waze community has a stricter policy about external reference sources, consult them before using this script.

## Requirements

- Tampermonkey or a compatible userscript manager.
- Access to the Waze Map Editor.

## Install

1. Open `wme-sv-road-assistant-1.0.0-beta.user.js` in this repository and click **Raw**.
2. Confirm the installation in Tampermonkey.
3. Reload WME.

The script is not on Greasyfork, so updates are manual: repeat the steps above for a new version.

## Usage

1. Zoom in to level 16 or higher.
2. Click **Scan**.
3. Tick the candidates you want.
4. Click **Convert**.
5. For each draft: align it to the road centerline, verify it, connect it to the road network, then press WME's **Save**.

**Undo:** use **Remove** on a candidate, or Ctrl+Z in WME for converted drafts that are still unsaved.

Running **Scan** again can join new pieces onto existing candidates or split them at junctions. Do your review after the last scan, and re-check junction dots and A/B ends if you scan again.

## Buttons

| Button | What it does |
|---|---|
| Scan | Finds candidates in the current view. |
| Convert | Creates the ticked candidates as new drafts in WME. Nothing is saved. |
| Remove | Deletes ticked candidates from the list. |
| Flip (⇆) | Swaps the A and B ends of a candidate. Propagates along a chain of joined candidates. |
| 1W / 2W | Switches a candidate between one-way (A to B) and two-way. |
| Zoom (⌖) | Zooms the map to a candidate. |

## Seam column

Each candidate row in the table has a **Seam** column showing what is at each end of the line:

| Badge | Meaning |
|---|---|
| **Blue dot** | That end is a junction — the trace was cut here because of an existing road or fork. |
| **✓** | Clean flow at that end — the candidate's orientation matches the adjacent segment. |
| **⚠ old #123** | Flow conflict with an existing segment that the script cannot reverse. You may need to fix this by hand in WME. |

A row can show a mix (e.g. **✓** on one end, **⚠ old #123** on the other). Blue dots are independent of the ✓/⚠ badges — they indicate *where the trace was cut*, not *whether flow matches*.

## Known limitations

- Drafts are not snapped to any GPS data. Align them by hand.
- Junction and seam flags rely on the mapped segments next to a candidate. Odd geometry can give a wrong dot or badge, so check each one.
- Seam badges are not refreshed after a flip. The badge reflects the candidate's state at creation time; the converted draft is unaffected.
- Large areas are slow to scan. Work in smaller views.
- The Settings panel collapses when a setting is changed. Reopen it to adjust another setting.

## Settings

Settings are saved in `localStorage` under `wme-svra-settings`.

| Setting | Default | What it does |
|---|---|---|
| Min map zoom to scan | 16 | Auto-zooms to this level before scanning |
| Zoom button level | 18 | Zoom level when you click the ⌖ button |
| Detection radius (m) | 20 | How far from existing roads a cell is treated as "free" |
| Min candidate length (m) | 30 | Shorter traces are discarded |
| Max candidates stored | 1000 | Storage cap per session |
| Sub-batch size | 10 | Drafts per internal batch during Convert |
| Max drafts per click | 25 | Hard cap on how many drafts one Convert click can produce |
| WME save-warning threshold | 250 | Warns when pending edits approach this count (never blocks) |
| Auto-continue sub-batches | on | If on, one Convert click runs sub-batches up to the per-click cap |
| Pause between batches (ms) | 2000 | WME settle time between sub-batches |
| Delay between segments (ms) | 300 | Per-segment delay |
| High-lock buffer (m) | 5 | Buffer around high-lock / protected segments |
| Road type | Street | The road type assigned to converted drafts |
| Split candidates at junctions | on | Splits traced candidates at existing-road crossings and skeleton forks |
| Auto scan resolution | on | Picks the scan zoom automatically based on viewport size |
| Tile budget per scan | 40 | Max coverage tiles fetched per scan |
| Auto show/hide coverage with Street View | on | Toggles the green coverage layer with the Street View panel |
| Responsibility acknowledgement | off | Must be checked once before Convert is enabled |

## About

Written by **dinkaftac** ([GitHub: 9w2xne](https://github.com/9w2xne)). AI tools helped with code review and documentation; the design decisions, safety boundaries, and final code are the author's.

## Local rules first

Before using this script, check with your local Waze community. Some communities restrict external reference sources (including Street View tiles) more strictly than the global guidelines. If your community says no, don't use it — even if the script works technically.

## Disclaimer

This is a review-and-approve tool, not an automated editor. You are responsible for every segment you save. Follow your local Waze community's editing guidelines.

## License

MIT. See [LICENSE](LICENSE).
