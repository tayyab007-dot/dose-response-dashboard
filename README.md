# Drug Dose-Response Dashboard (BBBC021)

**Category:** Tool & Platform
**Challenge:** AI for Life Science (5th Pazhou Algorithm Competition)

A tool that counts cell nuclei in microscope images, measures how cell
numbers change with drug concentration, and reports an honest toxicity
verdict per compound through an interactive dashboard.

## Run it (free, no GPU, no login)
1. [Open in Colab](https://colab.research.google.com/github/<USERNAME>/dose-response-dashboard/blob/main/dose_response.ipynb)
2. Runtime -> Run all (about 6-8 minutes; downloads about 800 MB)
3. Open the Gradio link printed at the end of the cell, pick a
   compound, press Submit.

Entry point: `dose_response.ipynb`. Pre-computed outputs: `results/`.
The Gradio link is temporary and only works while the notebook runs.

## Data
BBBC021v1 (MCF-7 breast cancer cells, treated 24 h, DNA/F-actin/
beta-tubulin staining), plate Week4_27481: 240 images, 8 compounds.
"We used image set BBBC021v1 [Caie et al., Molecular Cancer
Therapeutics, 2010], available from the Broad Bioimage Benchmark
Collection [Ljosa et al., Nature Methods, 2012]."
Images and ground truth are copyright AstraZeneca Pharmaceuticals. The
notebook downloads them from the BBBC site; this repo does not
redistribute them. Concentration units are not stated on the BBBC021
page, so plots use "BBBC021 units". Taxol is excluded (one concentration).

## Method
1. Segment nuclei in the DAPI channel (Gaussian blur, Otsu threshold,
   distance-transform watershed) and count cells per image.
2. Viability % = cell count / mean DMSO (control) count x 100.
3. Compare each dose with DMSO (one-sided Mann-Whitney U test).
4. Verdict: viability < 70% from some dose upward, with at least one
   dose at p < 0.05. IC50 (4-parameter logistic) is shown only if R2 >= 0.5.

## Results
- Anisomycin: dose-dependent toxicity from 0.3 (IC50 about 0.23)
- Tunicamycin: toxic only at the highest dose (50)
- 5-fluorouracil, AG-1478, indirubin monoxime, olomoucine:
  no clear effect in the tested range

Screenshots: `docs/`. Output tables: `results/`.

## Limitations
- One plate, 4 images per dose; control variation about 57%
- Very toxic, inactive or QC-failed doses were removed by the dataset
  authors, so some concentrations are missing
- Touching nuclei can be merged (undercounting)
- Decision rule was refined after seeing initial results (per-dose test
  was underpowered); 48 tests run, so false positives are possible
- Risk labels are illustrative, not clinical
