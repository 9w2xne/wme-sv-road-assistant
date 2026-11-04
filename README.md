# wme-sv-road-assistant
Manual-assistance userscript for WME — proposes draft segments over Street View coverage gaps. Never saves, never edits existing map data.

# WME Street View Road Assistant

A userscript for the Waze Map Editor (WME) that proposes **draft segments** over Street View coverage gaps. Every draft must be reviewed, aligned, and confirmed by the editor before it is saved.

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
