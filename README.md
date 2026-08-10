# seestar-siril-scripts

Free, ready-to-run [Siril](https://siril.org/) scripts for stacking Seestar S50
deep-sky subs captured through N.I.N.A. Each script takes you from raw FITS
frames all the way to a finished master — calibration, plate solving,
registration, and stacking — and every one is heavily commented so you can tune
it to your data.

**➡️ [Download the latest scripts from Releases](https://github.com/tbgh011/seestar-siril-scripts/releases/latest)**

## Requirements

- **Siril 1.3.6 or newer**
- Subs captured with a Seestar S50 through N.I.N.A (raw FITS frames)

## Setup

Put your frames in a working directory with these subfolders, then run the
script from Siril:

```
my-target/
├── lights/    # your light frames (always required)
├── darks/     # dark frames    (calibrated variants)
├── biases/    # bias frames    (calibrated variants)
└── flats/     # flat frames    (flats variant only)
```

Only add the folders your chosen script needs — **Lights Only** variants need
just `lights/`. Folder names must match exactly (note `biases/`, plural). If a
download opens in the browser instead of saving, right-click it and choose
**"Save link as…"**.

## Which script do I use?

Pick a **single-target** script if your object fits in one field, or a
**mosaic** script if your subs tile across the sky and stitch together.

Every script stacks your **lights** — the script name lists the calibration
frames it adds, so only the **Lights Only** variants skip calibration frames.

### Single-target · one field

| Script | Best for |
| --- | --- |
| **Lights + Biases + Darks + Flats** ⭐ *recommended* | The most complete calibration. Adds **flats** to correct vignetting and dust shadows on top of bias + matched-dark subtraction and cosmetic hot-pixel correction, then registers and stacks. No background extraction — run BGE yourself on the finished master. Needs bias, dark, **and flat** frames. |
| **Lights + Biases + Darks + BGE** | The best choice when you don't shoot flats. Full calibration (dark optimization + cosmetic correction), removes the sky gradient from every sub, then runs roundness-filtered registration and stacking. Use when you have matched bias and dark frames. |
| **Lights + Biases + Darks** | Same calibration and stacking, but **skips** background extraction so each frame's gradient stays intact. Choose this if you prefer to remove gradients yourself later. |
| **Lights Only** | No calibration frames — plate solving, roundness filtering, and sigma rejection at the stack are the only hot-pixel cleanup. Rejection relies on dither moving hot pixels between frames, so use this **only when you dithered**. |

### Mosaic · multi-panel

| Script | Best for |
| --- | --- |
| **Lights + Biases + Darks + BGE** ⭐ *recommended* | The everyday mosaic pipeline. Full calibration + background extraction flattens every tile before stitching so panel borders don't show, then grows the canvas and feathers the seams. Skips overlap normalization, so it's far faster on large panel counts. |
| **Lights + Overlap-Norm + Biases + Darks + BGE** | The highest-quality mosaic, for a final render. Same calibration + BGE **plus overlap normalization** that matches panel levels using only their shared regions for truly seamless joins. This pass dominates runtime — budget well over an hour on 1,000+ frames. |
| **Lights + Biases + Darks** | A calibrated mosaic **without** background extraction to pre-flatten tiles, so seams are more likely. Switch to an overlap-norm variant if panel boundaries show on harder data. |
| **Lights Only** | Stitches a mosaic from lights alone, no calibration frames. Plate-solves every tile, grows the canvas, and feathers seams, with sigma rejection as the only hot-pixel removal. Simplest to run, but expect some hot-pixel survivors on warm nights. |

## Links

- 🎥 [Astro Imager on YouTube](https://www.youtube.com/@AstroImager-Capture-Processing) — walkthroughs and live processing
- 🌐 [astro-imager.com](https://astro-imager.com)
