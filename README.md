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
└── flats/     # flat frames    (every calibrated mosaic + single-target Flats)
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

**Why flats matter:** vignetting is multiplicative and radial — darker corners
on every sub. Stack normalization, a background fit, overlap normalization, and
feathering all adjust levels or planes, so none of them can remove it; only
flats can. In a mosaic those dark corners line up with the panel borders and
show as seams, which is why every calibrated mosaic script uses flats as of
v1.3.

### Single-target · one field

| Script | Best for |
| --- | --- |
| **Lights + Biases + Darks + Flats** ⭐ *recommended* | The most complete calibration. **Flats** correct vignetting and dust shadows on top of bias subtraction, dark optimization, and cosmetic hot-pixel correction, then plate-solve, roundness-filtered registration, and stacking. No background extraction — run BGE yourself on the finished master. Needs bias, dark, **and flat** frames. |
| **Lights + Biases + Darks + BGE** | The best choice when you don't shoot flats. Full calibration, removes the sky gradient from every sub, then registers and stacks. Vignetting stays in the stack as a falloff toward the edges. |
| **Lights + Biases + Darks** | Same calibration and stacking, but **skips** background extraction so each frame's gradient stays intact. Choose this if you prefer to remove gradients yourself later. |
| **Lights Only** | No calibration frames — plate solving, roundness filtering, and sigma rejection at the stack are the only hot-pixel cleanup. Rejection relies on dither moving hot pixels between frames, so use this **only when you dithered** (or when a meridian flip rotated half the session). |

### Mosaic · multi-panel

Every calibrated mosaic script needs `biases/`, `darks/`, **and** `flats/`.

| Script | Best for |
| --- | --- |
| **Lights + Biases + Darks + Flats + BGE · Feather 50** ⭐ *recommended* | The everyday mosaic pipeline. Full calibration with flats, then background extraction flattens every tile before stitching, and a 50px feather blends the seams. Skips overlap normalization, so it's fast on large panel counts. Best when the target fills only part of each frame. |
| **Overlap-Norm + Lights + Biases + Darks + Flats · Feather 50** ⭐ *recommended* | For **large targets that fill the frame** (M31, big nebulae). Per-tile BGE is off by default, because on those targets the background plane follows the target instead of the sky and carves hard seams and dark halos that overlap normalization can't repair. Overlap normalization and a 50px feather match the panels instead; do BGE once on the finished mosaic. Slow — same runtime warning as above. |
| **Lights + Biases + Darks + Flats · Feather 15** | Same calibration with flats, but **no** per-tile background extraction, so each tile keeps its own sky gradient and seams are more likely. Use it when you'd rather do BGE once on the finished mosaic. Read the script's sanity-check notes: with no BGE, a mis-built master dark won't announce itself. |
| **Overlap-Norm + Lights + Biases + Darks + Flats + BGE · Feather 15** | The highest-quality mosaic for small-to-medium targets, for a final render. Same as the recommended script **plus overlap normalization**, which matches panel levels using only their shared regions. This pass dominates runtime — budget well over an hour on 1,000+ frames. |
| **Overlap-Norm + Lights + Biases + Darks + Flats + BGE · Feather 50** | Identical to the Feather 15 script above, but blends panel edges over 50px instead of 15px. The wider taper spreads any level mismatch that survives overlap normalization across more pixels, so seams are harder to see, and it costs no extra runtime. Start here; drop to Feather 15 if the wide blend visibly softens detail near the joins. |
| **Lights Only** | Stitches a mosaic from lights alone, no calibration frames. Plate-solves every tile, grows the canvas, and feathers seams, with sigma rejection as the only hot-pixel removal. Simplest to run, but expect hot-pixel survivors on warm nights and vignetting seams at panel borders. |

**Overlap-norm variants need colour calibration afterwards.** Siril applies
`-rgb_equal` only in its ordinary normalization path, never alongside
`-overlap_norm` — the two are mutually exclusive branches, so the flag is
silently ignored while the log still reports *RGB equalization ... enabled*.
Both overlap-norm scripts therefore leave it off, and their stacks come out
with the usual OSC green cast — roughly 20% green over red on Seestar data.
Run **SPCC** on the finished mosaic (*Image Processing → Color Calibration →
Spectrophotometric*; the stack keeps its WCS, so no re-solve is needed), or
`rmgreen` for a quick cosmetic fix. Do it **before** background extraction.
The other mosaic scripts apply `-rgb_equal` normally and are unaffected.

### Output filenames

Each script saves its result in your working directory, named from the FITS
headers — object, frame count, exposure, sensor temperature, and date — with a
tag that tells you which script made it:

| Tag | Script |
| --- | --- |
| `_FLAT_` | Single-target Lights + Biases + Darks + Flats |
| `_BGE_` | Single-target Lights + Biases + Darks + BGE |
| `_cal_` | Single-target Lights + Biases + Darks |
| `_nocal_` | Lights Only (single-target or mosaic) |
| `_FLAT-BGE_` | Mosaic Lights + Biases + Darks + Flats + BGE |
| `_FLAT-cal_` | Mosaic Lights + Biases + Darks + Flats |
| `_NORM-BGE-ovl_` | Mosaic Overlap-Norm + Flats + BGE · Feather 15 |
| `_NORM-BGE-ovl-f50_` | Mosaic Overlap-Norm + Flats + BGE · Feather 50 |
| `_NORM-ovl_` | Mosaic Overlap-Norm + Flats · Feather 50 |

## Links

- 🎥 [Astro Imager on YouTube](https://www.youtube.com/@AstroImager-Capture-Processing) — walkthroughs and live processing
- 🌐 [astro-imager.com](https://astro-imager.com)
