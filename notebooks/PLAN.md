# Plan: NHANES Population Baseline Integration for Astronaut Mineral/CBC Analysis

## Summary

Integrate NHANES population reference data (3 pooled cycles: 2013-2014, 2015-2016, 2017-2018) to contextualize the 4-astronaut mineral/CBC clinical measurements against a large population baseline. This addresses the fundamental limitation of n=4 within-subject comparisons by providing external population reference intervals, z-scores, percentile ranks, and formal one-sample tests. The integration covers 15 matched variables across biochemistry (Ca, K, Na, Cl, CO2) and hematology (Hb, Hct, RBC, MCH, MCHC, MCV, WBC, RDW, platelets, MPV).

## Data Sources

### NHANES (downloaded from CDC)
- **Cycles**: 2013-2014 (_H), 2015-2016 (_I), 2017-2018 (_J)
- **Files per cycle**:
  - Standard Biochemistry Profile: BIOPRO_H, BIOPRO_I, BIOPRO_J
  - Complete Blood Count: CBC_H, CBC_I, CBC_J
  - Demographics: DEMO_H, DEMO_I, DEMO_J
- **Download URL pattern**: `https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/{year}/DataFiles/{FILENAME}.XPT`
- **Parsing**: `pandas.read_sas(format='xport')`

### Astronaut clinical data (existing)
- `astronaut_minerals_combined.csv`: 28 rows × 13 cols (4 crew × 7 timepoints)
- `astronaut_cbc_full.csv`: 28 rows × 25 cols (full CBC with differentials)
- Timepoints: L-92, L-44, L-3 (pre-flight), R+1 (flight/return), R+45, R+82, R+194 (post-flight)

### Variable Mapping (15 variables)

| Astronaut Variable | NHANES File | NHANES Code | Description | Units |
|---|---|---|---|---|
| CALCIUM | BIOPRO | LBXSCA | Total Calcium | mg/dL |
| POTASSIUM | BIOPRO | LBXSKSI | Potassium | mmol/L |
| SODIUM | BIOPRO | LBXSNASI | Sodium | mmol/L |
| CHLORIDE | BIOPRO | LBXSCLSI | Chloride | mmol/L |
| CARBON DIOXIDE | BIOPRO | LBXSC3SI | Bicarbonate | mmol/L |
| HEMOGLOBIN | CBC | LBXHGB | Hemoglobin | g/dL |
| HEMATOCRIT | CBC | LBXHCT | Hematocrit | % |
| RED BLOOD CELL COUNT | CBC | LBXRBCSI | RBC count | M/uL |
| MCH | CBC | LBXMCHSI | Mean cell Hgb | pg |
| MCHC | CBC | LBXMC | Mean Cell Hgb Conc | g/dL |
| MCV | CBC | LBXMCVSI | Mean cell volume | fL |
| WHITE BLOOD CELL COUNT | CBC | LBXWBCSI | WBC count | 10³/uL |
| RDW | CBC | LBXRDW | RBC distribution width | % |
| PLATELET COUNT | CBC | LBXPLTSI | Platelet count | 10³/uL |
| MPV | CBC | LBXMPSI | Mean platelet volume | fL |

## Population Reference Construction

### Filtering
- **Age**: 40-60 years (RIDAGEYR)
- **Sex**: All sexes (RIAGENDR 1=Male, 2=Female) — included as a covariate
- **Expected pooled N**: ~5,000+ participants across 3 cycles (age 40-60 with lab data)

### Reference Statistics Computed (per variable, per sex, and combined)
- Mean, SD, median
- Percentiles: P1, P2.5, P5, P10, P25, P50, P75, P90, P95, P97.5, P99
- 95% reference interval (P2.5-P97.5)
- Sample size (N with non-missing values)

## Analysis Methods

### 1. Z-score normalization
For each astronaut measurement:
```
z = (astronaut_value - NHANES_mean) / NHANES_SD
```
Computed against the age 40-60 all-sexes reference. Also computed for sex-specific reference as sensitivity analysis.

### 2. Reference interval overlay
P2.5-P97.5 bands from NHANES overlaid on astronaut trajectory plots. Measurements falling outside the band are flagged as "outside population reference range."

### 3. Percentile rank
Each astronaut measurement converted to its percentile rank in the NHANES reference distribution (empirical CDF). E.g., "Calcium at R+1 is at the 96th percentile."

### 4. One-sample tests
At each timepoint, test whether the mean astronaut value differs from the NHANES population mean:
- **One-sample t-test**: astronaut values vs NHANES mean (H₀: astronaut mean = NHANES mean)
- **One-sample Wilcoxon signed-rank test**: non-parametric alternative
- **Effect size**: Cohen's d = (astronaut_mean - NHANES_mean) / NHANES_SD
- **Multiple testing correction**: Benjamini-Hochberg FDR across 15 variables × 7 timepoints = 105 tests
- Note: With n=4 astronauts, these tests are underpowered — we report them with appropriate caveats and emphasize effect sizes and z-score patterns over p-values

### 5. Time-resolved z-score trajectory analysis
- Track z-score trajectories across 7 timepoints for each variable
- Identify which variables show sustained population deviation vs transient shifts
- Classify each variable-timepoint as: within normal range (|z| < 1.96), borderline (1.96 ≤ |z| < 2.58), or significantly deviating (|z| ≥ 2.58)

## Deliverables

### Figures (SVG + PNG, saved to `/mnt/results/figures/` and copied to Zenodo + manuscript)
1. **Fig S6: Astronaut mineral/CBC trajectories with NHANES reference bands**
   - 15 panels (one per variable), arranged in a grid
   - X-axis: 7 timepoints (L-92 through R+194)
   - Y-axis: clinical value with units
   - Shaded band: NHANES P2.5-P97.5 reference interval (age 40-60, all sexes)
   - Lines: 4 individual astronauts + mean trajectory
   - Color: Phylo palette, colorblind-friendly
2. **Fig S7: Z-score heatmap**
   - Rows: 15 variables, Columns: 7 timepoints
   - Cell values: mean astronaut z-score (against NHANES reference)
   - Diverging colormap (blue-white-red), centered at 0
   - Annotations: |z| ≥ 1.96 outlined, |z| ≥ 2.58 bold
3. **Fig S8: Percentile rank dot plot**
   - X-axis: 15 variables, Y-axis: percentile rank (0-100)
   - Dots for each astronaut at each timepoint, colored by timepoint
   - Reference bands at P2.5, P5, P95, P97.5

### Tables (CSV, saved to `/mnt/results/data/`)
1. `nhanes_reference_stats.csv` — per-variable, per-sex reference statistics (N, mean, SD, percentiles)
2. `astronaut_zscores.csv` — 28 rows × 15 z-score columns + timepoint + subject ID
3. `astronaut_percentile_ranks.csv` — 28 rows × 15 percentile columns + timepoint + subject ID
4. `nhanes_onesample_tests.csv` — 105 rows (15 vars × 7 timepoints): t-stat, p-value, Wilcoxon p, FDR-adjusted p, Cohen's d, mean z, % outside reference

### Manuscript text update
- New supplementary section: "Population-referenced analysis of astronaut clinical markers"
- ~300-400 words describing the NHANES integration, key findings, and interpretation
- Updated results discussion referencing the new figures and tables

### Script
- `11_nhanes_integration.py` in `/workspace/osdr_analysis/`, copied to `zenodo_package/code/`

## Implementation Steps

1. **Download NHANES XPT files** for 3 cycles (BIOPRO, CBC, DEMO for _H, _I, _J) — 9 files total
2. **Parse and pool** all 3 cycles into a single DataFrame, merge demographics with labs on SEQN
3. **Filter to age 40-60**, compute reference statistics per variable
4. **Load astronaut data**, merge minerals + CBC into a single 28-row dataset
5. **Compute z-scores** for all 15 variables × 28 measurements
6. **Compute percentile ranks** using empirical CDF
7. **Run one-sample tests** (t-test + Wilcoxon) at each timepoint, apply FDR correction
8. **Generate Fig S6**: trajectory plots with reference bands
9. **Generate Fig S7**: z-score heatmap
10. **Generate Fig S8**: percentile rank dot plot
11. **Save all tables** to `/mnt/results/data/`
12. **Copy figures** to Zenodo package and manuscript directories
13. **Write manuscript text** update and save to `/mnt/results/`

## Compute Estimate

- NHANES download: 9 XPT files, ~2-3 MB each — < 1 minute
- Parsing and pooling: trivial pandas operations — seconds
- Z-scores, percentile ranks, one-sample tests: trivial — seconds
- Figure generation: 3 multi-panel figures — < 1 minute
- Total runtime: < 5 minutes on default `worker-0` machine
- No HPC or additional machines needed

## Assumptions & Caveats

- **Astronaut demographics**: Exact age and sex of crew members (C001-C004) are not in the provided data. We assume ages 40-60 and use all-sexes reference as primary, with sex-specific as sensitivity analysis. If crew demographics are available, the reference can be refined.
- **Unit compatibility**: NHANES SI-unit variables (mmol/L for electrolytes) are compatible with astronaut units (mEq/L = mmol/L for monovalent ions). Calcium is mg/dL in both. Hematology units match directly.
- **Survey weights**: NHANES uses complex survey design. For reference interval estimation, we use unweighted statistics (standard practice for reference range derivation). Survey-weighted means would differ minimally for these standard lab values.
- **Pooling across cycles**: Lab methods are consistent across 2013-2018 cycles (same Beckman Coulter methodology). Pooling increases N and stability of reference intervals.
- **Statistical power**: With n=4 astronauts, one-sample tests at each timepoint are severely underpowered. We emphasize effect sizes (Cohen's d, z-scores) and population deviation patterns over p-values. The FDR correction is applied for completeness but the analysis is inherently discovery-oriented.
- **No causal inference**: This integration provides descriptive population context, not causal claims about spaceflight effects.
