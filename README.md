# vasc_enface_choroidal_analizer_UWF
Quantitative widefield OCTA en face analysis of choroidal vortex venous system. ARVO 2026 abstract by Pedro Valls Alonso @pedrovaalo, Spain. 

# OCTA Batch Analysis — v2

MATLAB script for automated batch analysis of **Canon Xephilio OCT-A** images. It segments the retinal vasculature, computes morphological metrics per quadrant, and exports all results to an Excel file.

> Developed and tested on Canon Xephilio OCT-A exports (`.jpg`, `.png`, `.tif`).

---

## Requirements

- MATLAB with the following toolboxes:
  - **Image Processing Toolbox**
  - **Statistics and Machine Learning Toolbox**
- Images exported from Canon Xephilio in `.jpg`, `.png`, or `.tif` format

---

## How to use

1. Open the script in MATLAB and run it (`Run` or `F5`).
2. A dialog will appear: **select the folder** containing the images.
3. For each image, the script will prompt you to:
   - **Click on the center of the optic disc** (used as the origin for quadrant division).
   - **Select whether the eye is OD (right) or OS (left)**.
4. When finished, all outputs are saved in the same folder.

---

## Processing pipeline

| Step | Description |
|------|-------------|
| 1 | Image reading and normalization |
| 2 | Valid field mask (removes black background) |
| 3 | Contrast enhancement (adaptive CLAHE) |
| 4 | Gentle Gaussian filtering (preserves fine capillaries) |
| 5 | Vascular binary segmentation (adaptive thresholding) |
| 6–8 | Interactive disc centre selection and eye laterality |
| 9 | Division into 4 quadrants: ST, SN, IT, IN |
| 10 | Vascular Index (VI) — global and per quadrant |
| 11–12 | Vascular skeleton, length and real vessel diameter (bwdist) |
| 13 | Branch points, end points and bifurcation density |
| 14 | Fractal dimension (vectorised box-counting) |
| 15–16 | Pachyvessel detection and vortex watershed crossings |
| 17–19 | Overlays and summary multipanel figure |
| 20–21 | Console output and cumulative results table |
| 22 | Output image saving |

---

## Output files

All files are saved in the same folder as the input images:

| File | Content |
|------|---------|
| `resultado_binario_<name>.png` | Binary vascular map |
| `resultado_skeleton_<name>.png` | Vascular skeleton |
| `resultado_pachyvessels_<name>.png` | Pachyvessel map |
| `resultado_overlay_pachyvessels_<name>.png` | Pachyvessel overlay on original image |
| `resultado_overlay_combo_<name>.png` | Combined overlay (capillaries + pachyvessels) |
| `resultado_overlay_skeleton_<name>.png` | Skeleton overlay |
| `resultado_multipanel_<name>.png` | Summary multipanel figure (300 dpi) |
| `resultados_OCTA_batch_v2.xlsx` | Table with all metrics for all processed images |

---

## Metrics

| Metric | Description |
|--------|-------------|
| `VI_global` | Global vascular index (vessel area fraction) |
| `VI_ST/SN/IT/IN` | VI per quadrant (SuperoTemporal, SuperoNasal, InferoTemporal, InferoNasal) |
| `DominantQuadrant` | Quadrant with highest VI |
| `AsymmetryRatio` / `AsymmetryDifference` | Inter-quadrant asymmetry |
| `VesselLength_px` | Total skeleton length in pixels |
| `MeanDiameter_px` | Mean real vessel diameter — **main metric in v2** |
| `StdDiameter_px` | Vessel calibre variability |
| `MaxDiameter_px` | Maximum calibre (pachyvessel indicator) |
| `Diam_ST/SN/IT/IN` | Mean diameter per quadrant |
| `BranchPoints` / `EndPoints` | Vascular bifurcation and terminal points |
| `BranchDensity_Mpx` | Bifurcation density per million pixels |
| `FractalDimension` | Vascular network complexity (box-counting) |
| `LargeVesselFraction` | Area fraction occupied by large vessels |
| `VerticalCrossings` / `HorizontalCrossings` / `TotalVortexCrossings` | Vessels crossing the disc axes (inter-vortex anastomoses) |
| `NumAnastomosisMarked` | Number of detected and marked anastomoses |
| `VDI` | Area/length index (kept for v1 compatibility) |

---

## Changes from v1

- Adaptive thresholding restored (Otsu global was less reliable on Canon Xephilio en face OCTA)
- **Real vessel diameter** computed via `bwdist` on the skeleton, replacing the VDI proxy
- `adapthisteq` with ClipLimit 0.02 and NumTiles `[8 8]`
- `imgaussfilt` instead of `medfilt2` (preserves 1–2 px capillaries)
- `bwareaopen` threshold reduced from 50 to **15 px** (retains short capillary segments)
- **Vectorised box-counting** (~30× faster)
- `writetable` moved **outside the loop** (safer, avoids mid-run file corruption)
- Vector broadcasting instead of `meshgrid` (lower memory footprint)

---

## Notes

- If the output Excel file already exists, new results are **appended**, not overwritten.
- The script requires **manual interaction per image** (disc click + eye selection). It is not fully automated.
- Centre coordinates (`CenterX`, `CenterY`) are stored in the Excel file for traceability.
- Images must be **pre-exported from Canon Xephilio** as standard image files before running the script. Raw `.img` or proprietary formats are not supported.
