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
Put your frames in a working directory with these subfolders…
    my-target/
    ├── lights/    # always required
    ├── darks/     # calibrated variants only
    └── bias/      # calibrated variants only

## Which script do I use?
[single-target table + mosaic table with your descriptions]
