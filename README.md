# Occult CKD Detection Pipeline

Analysis code for:

> Ishida H, Yonetamari J, Ishida M, Tachikawa M, Kanbe A, Shirakami Y, Watanabe T, Okura H, Yasuda Y, Kikuchi R.
> **Interpretable Prediction Model for Occult CKD Detection.**
> *Kidney International Reports* (2026). https://doi.org/[DOI]

---

## Overview

This repository contains the complete analysis pipeline for developing and temporally validating an interpretable logistic regression model that uses six routinely available laboratory variables to prioritize cystatin C testing in patients with preserved creatinine-based eGFR.

**Occult CKD** is defined as eGFRcys < 60 mL/min/1.73 m² with eGFRcr ≥ 60 mL/min/1.73 m².

**Proposed model predictors:** Age, Sex, Serum creatinine (Cre), Blood urea nitrogen (UN), Serum albumin (Alb), Hemoglobin (Hb)

---

## Repository Structure

```
.
├── OccultCKD_pipeline.ipynb   # Main analysis notebook
├── requirements.txt            # Python dependencies
├── README.md                   # This file
└── data_format.md              # Input data format specification
```

---

## Requirements

- Python 3.10 or later
- See `requirements.txt` for package dependencies

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Input Data

The pipeline requires a CSV file with the following columns:

| Column | Type    | Description                          | Unit        |
|--------|---------|--------------------------------------|-------------|
| Date   | string  | Measurement date (YYYY-MM-DD)        | —           |
| Age    | numeric | Patient age                          | years       |
| Sex    | numeric | Sex (1 = Male, 0 = Female)           | —           |
| Cre    | numeric | Serum creatinine                     | mg/dL       |
| CysC   | numeric | Serum cystatin C                     | mg/L        |
| UN     | numeric | Blood urea nitrogen                  | mg/dL       |
| Alb    | numeric | Serum albumin                        | g/dL        |
| Hb     | numeric | Hemoglobin                           | g/dL        |
| HT     | numeric | Height (optional, for subgroup BMI)  | cm or m     |
| BW     | numeric | Body weight (optional)               | kg          |

> **Note:** Patient data from Gifu University Hospital are not publicly available due to institutional and ethical restrictions. Please see the Data Availability section of the manuscript for data access requests.

See `data_format.md` for detailed format specifications.

---

## Usage

### Running in Jupyter Notebook

1. Open `OccultCKD_pipeline.ipynb`
2. Edit the user settings cell:

```python
INPUT_CSV  = "your_data.csv"   # Path to your input CSV
OUTDIR     = "out_final"        # Output directory
RUN_MODE   = "both"             # "occult", "cys60", or "both"
CUTOFF_DATE = "2025-01-01"     # Temporal split date
```

3. Run all cells

### Running as a Script

The notebook can also be executed via command line after exporting to `.py`:

```bash
jupyter nbconvert --to script OccultCKD_pipeline.ipynb
python OccultCKD_pipeline.py \
    --input your_data.csv \
    --outdir out_final \
    --run_mode both \
    --cutoff_date 2025-01-01
```

---

## Outputs

All outputs are CSV files for use in figure generation:

### Main Analysis (Occult CKD)

| File | Description |
|------|-------------|
| `Figure1_FlowCounts_occult.csv` | Study flowchart counts |
| `Table1_Baseline_PrimaryCohort.csv` | Baseline characteristics |
| `Figure2_ROC_External_occult.csv` | ROC curve data |
| `Table2_ExternalPerformance_occult.csv` | Performance metrics (AUC, sensitivity, specificity, etc.) |
| `Figure3_TopFrac_Subgroup_occult.csv` | Resource-constrained screening sensitivity |
| `Figure4_LogitCoefs_occult.csv` | Standardized model coefficients with bootstrap CI |
| `Figure5_DCA_occult.csv` | Decision curve analysis |
| `Figure5_Calibration_occult.csv` | Calibration plot data |
| `SupplementaryTableS1_LogitParams.csv` | Implementation parameters (means, SDs, coefficients) |

### Secondary Analysis (eGFRcys < 60, entire population)

| File | Description |
|------|-------------|
| `SupplementaryTableS2a_Internal_byCys60.csv` | Internal cohort baseline by cys60 |
| `SupplementaryTableS2b_External_byCys60.csv` | External cohort baseline by cys60 |
| `SupplementaryTableS3_ExternalPerformance_cys60.csv` | Secondary performance metrics |
| `SupplementaryFigureS1_ROC_External_cys60.csv` | Secondary ROC data |
| `SupplementaryFigureS2_TopFrac_Subgroup_cys60.csv` | Secondary screening sensitivity |

---

## Model Implementation

The proposed logistic regression model can be implemented using the parameters in `SupplementaryTableS1_LogitParams.csv`:

```
For an individual with features x = [Age, Sex, Cre, UN, Alb, Hb]:

  z = Σ β_i × (x_i − Mean_i) / SD_i + Intercept
  p = 1 / (1 + exp(−z))
```

where Mean_i, SD_i, and β_i are provided in Supplementary Table S1.

---

## eGFR Equations

This pipeline uses Japanese Society of Nephrology (JSN) equations:

- **eGFRcr**: 194 × Cr^−1.094 × Age^−0.287 (× 0.739 for females)
- **eGFRcys**: (104 × CysC^−1.019 × 0.996^Age × [0.929 for females]) − 8
- **eGFReq5**: 142 × Cr^−0.923 × Age^−0.185 × Alb^0.414 × UN^−0.233 (× 0.772 for females)
- **eGFRindiv**: eGFRcr × BSA / 1.73 (Du Bois formula)

---

## Citation

If you use this code, please cite:

```
Ishida H, et al. Interpretable Prediction Model for Occult CKD Detection.
Kidney International Reports. 2026. https://doi.org/[DOI]
```

---

## License

This code is provided for academic and research use.

---

## Contact

Ryosuke Kikuchi, Ph.D.
Division of Clinical Laboratory, Gifu University Hospital
kikuchi.ryosuke.f7@f.gifu-u.ac.jp
