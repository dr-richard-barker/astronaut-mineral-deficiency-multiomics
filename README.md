# 🚀 Mineral-Pathway Transcriptional and Molecular Responses to Spaceflight: A Multi-Omics Analysis of Astronaut and Rodent Data

> [!IMPORTANT]
> **This repository is archived.** It is an early version of the analysis (September 2026).
> The current version, with corrected gene sets, citations and claims, is
> **[astronaut-mineral-final](https://github.com/dr-richard-barker/astronaut-mineral-final)**
> ([project page](https://dr-richard-barker.github.io/astronaut-mineral-final/)).

[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.XXXXXXX-blue)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: MIT](https://img.shields.io/badge/Code-MIT-green.svg)](LICENSE)
[![License: CC BY 4.0](https://img.shields.io/badge/Data_%26_Docs-CC_BY_4.0-lightgrey.svg)](LICENSE-CC-BY-4.0)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-3776AB.svg)](https://www.python.org/)
[![Data: NASA OSDR](https://img.shields.io/badge/Data-NASA_OSDR_Inspiration4_%26_Rodents-orange.svg)](https://osdr.nasa.gov/)
[![Reference: CDC NHANES](https://img.shields.io/badge/Reference-CDC_NHANES_2013--2018-blueviolet.svg)](https://wwwn.cdc.gov/nchs/nhanes/)

**Comprehensive multi-omics analysis, machine learning prediction pipeline, population baseline integration, and FAIR research repository investigating mineral-pathway perturbations in astronauts and rodents during spaceflight.**

> **Author:** Richard Barker · NASA GeneLab / OSDR, Purdue University  
> **Target Journal:** *npj Microgravity*  
> **Data Sources:** NASA OSDR (Human Inspiration4 & Rodent Research missions) and CDC NHANES  

---

## 🔬 Scientific Overview

Prolonged spaceflight imposes profound physiological stresses including fluid redistribution, musculoskeletal atrophy, and altered mineral homeostasis. While gross bone demineralization and electrolyte shifts have been clinically noted since early space exploration, the molecular, transcriptional, and cross-species regulatory networks governing mineral metabolism remain poorly characterized.

This repository provides an end-to-end, reproducible multi-omics framework mining the NASA Open Science Data Repository (OSDR) across astronaut (*Homo sapiens*) and rodent (*Mus musculus*) flight datasets, cross-referenced with curated KEGG mineral homeostasis pathways and contextualized against pooled US population reference baselines from the CDC National Health and Nutrition Examination Survey (NHANES).

---

## 🧪 Authentic Datasets Harmonized

### 1. Human Spaceflight (Inspiration4 Mission, 3-day LEO, OSD-569..656)
- **OSD-569**: Whole blood RNA-seq, Complete Blood Counts (CBC) across 7 longitudinal mission timepoints (L-92, L-44, L-3, R+1, R+45, R+82, R+194).
- **OSD-570**: PBMC single-nucleus RNA-seq / snATAC-seq.
- **OSD-571**: Plasma proteomics, metabolomics, cell-free DNA/RNA.
- **OSD-572**: Multi-site microbiome swabs.
- **OSD-575 / OSD-630 / OSD-656**: Associated clinical chemistry and comprehensive metabolic panels (CMP).

### 2. Rodent Spaceflight (Rodent Research Missions aboard ISS)
- **OSD-47 / OSD-48**: Rodent liver RNA-seq and transcriptomics under microgravity vs ground controls.
- **OSD-99 / OSD-100 / OSD-574**: Multi-tissue transcriptomics across musculoskeletal, splenic, and metabolic tissues.

### 3. Population Baseline Context (CDC NHANES 2013–2018)
- 3 pooled biennial cycles (`2013–2014`, `2015–2016`, `2017–2018`) covering >5,000 age-matched adults (40–60 years).
- Matched 15 clinical parameters spanning electrolytes ($\text{Ca}^{2+}$, $\text{K}^+$, $\text{Na}^+$, $\text{Cl}^-$, $\text{HCO}_3^-$) and hematology (Hemoglobin, Hematocrit, RBC, Platelets, WBC, MCV, MCH, MCHC, RDW, MPV).

---

## 📊 Key Scientific Discoveries

1. **Specific Mineral-Pathway Perturbations**:
   - 46 mineral-pathway genes demonstrated significant differential expression across RNA-seq and proteomics ($|\log_2\text{FC}| > 0.5$, $p < 0.05$).
2. **Copper Pathway Dysregulation**:
   - Amyloid precursor protein (*APP*, copper-binding) significantly up-regulated in plasma proteomics, while lysyl oxidase family cross-linking enzymes (*LOX*, *LOXL1*) were down-regulated.
3. **Calcium/Zinc-Binding Calgranulins Suppressed**:
   - Innate immune alarmins (*S100A8*, *S100A9*, *S100A12*, calprotectin complex) exhibited sharp down-regulation immediately upon return ($R+1$), consistent with altered calcium signaling and post-flight immune modulation.
4. **Calcium Homeostasis Alteration**:
   - Voltage-dependent L-type calcium channel alpha-1D subunit (*CACNA1D*) was down-regulated ($-2.14\,\log_2\text{FC}$) in whole-blood RNA-seq.
5. **Machine Learning Regression of Serum Minerals**:
   - Regularized Ridge and Linear Support Vector Regression (Linear SVR) models predicted serum potassium ($r = 0.50, R^2 = 0.12$, Ridge) and sodium ($r = 0.46, R^2 = 0.19$, Linear SVR) directly from peripheral blood mineral-pathway transcript levels under leave-one-out cross-validation.
6. **Cross-Species Concordance Gap**:
   - Direct orthologous comparison between human astronauts (3-day LEO) and rodents (21-day ISS) revealed minimal concordance ($r = 0.007$), demonstrating that short-duration human orbital missions capture acute stress responses that differ fundamentally from long-duration rodent adaptation.
7. **NHANES Population Normalization**:
   - Standardized z-scores and empirical percentiles against 5,000+ age-matched Americans confirm that several astronaut post-flight electrolyte shifts exceed the 95th population percentile despite returning toward baseline by $R+82$.

---

## 📂 Repository Layout

```
.
├── code/                              # Reproducible numbered analysis pipeline
│   ├── 01_fetch_osdr_data.py          # Discover and download OSDR raw datasets
│   ├── 01b_fetch_osdr_data.py         # Extended REST fetcher
│   ├── 01c_rodent_download.py         # Rodent RNA-seq study screening
│   ├── 03_mineral_pathway_genesets.py # KEGG mineral pathway extraction & curation
│   ├── 04_ensembl_to_symbol.py        # ENSEMBL to HGNC symbol mapping
│   ├── 05_visualizations.py           # Core publication figures
│   ├── 06_rodent_cross_species.py     # Mouse-to-human ortholog concordance
│   ├── 07_systems_biology_atlas.py    # Multi-omics systems biology atlas composite
│   ├── 08_supplementary_outputs.py    # Supplementary tables and figures
│   ├── 09_cross_species_meta.py       # Multi-tissue rodent meta-analysis
│   ├── 10_ml_imbalance_reanalysis.py  # Machine learning regression & classification
│   └── 11_nhanes_integration.py       # CDC NHANES population baseline integration
├── data/                              # Harmonized processed datasets (45 CSV/GMT files)
├── fair_deposit/                      # Zenodo / RO-Crate archival packaging & metadata
├── figures/                           # 35 publication and supplementary figures (PNG, SVG, PDF)
├── manuscript/                        # Publication LaTeX, Word docx, and compiled PDF
└── notebooks/                         # Computational execution trace and execution plan
```

---

## 🚀 Quickstart

```bash
# 1. Clone repository
git clone https://github.com/dr-richard-barker/astronaut-mineral-deficiency-multiomics.git
cd astronaut-mineral-deficiency-multiomics

# 2. Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run any analysis script
python code/05_visualizations.py
python code/10_ml_imbalance_reanalysis.py
python code/11_nhanes_integration.py
```

---

## 🌐 Ecosystem Integration & Synergies

This study is part of the space biology computational ecosystem developed across related projects:

- **[OSDR-Veggie-Nutrition-Dashboard](https://github.com/dr-richard-barker/OSDR-Veggie-Nutrition-Dashboard)**: Direct translational synergy—the human spaceflight mineral deficiencies documented here (calcium, potassium, magnesium, copper) define the nutritional targets for bio-regenerative crop biofortification (e.g. Outredgeous lettuce and Mizuna mustard grown aboard the ISS).
- **[BLSS_BioSim_Modeling](https://github.com/dr-richard-barker/BLSS_BioSim_Modeling)**: Life support elemental balancing—empirical astronaut mineral fluxes provide real human physiological parameters for closed-loop mass balance modeling.
- **[AIRI (AstroBotany Investigation Resource)](https://dr-richard-barker.github.io/AIRI/)**: Hands-on space omics and astrobotany curriculum bridging plant biology and human astronaut space health.

---

## 📜 License & Citation

- **Source Code**: [MIT License](LICENSE)
- **Data, Figures, and Text**: [Creative Commons Attribution 4.0 International (CC BY 4.0)](LICENSE-CC-BY-4.0)

Please cite this study and the underlying NASA OSDR and CDC NHANES datasets as detailed in [CITATION.cff](CITATION.cff).
