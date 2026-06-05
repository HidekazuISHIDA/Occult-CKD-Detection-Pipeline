# Input Data Format

## Required CSV Structure

The input file must be a comma-separated values (CSV) file with the following columns.

### Required Columns

| Column | Data Type | Description | Unit | Notes |
|--------|-----------|-------------|------|-------|
| `Date` | string | Measurement date | — | Format: YYYY-MM-DD |
| `Age` | numeric | Patient age | years | Must be > 0 |
| `Sex` | numeric | Biological sex | — | 1 = Male, 0 = Female |
| `Cre` | numeric | Serum creatinine | mg/dL | Must be > 0 |
| `CysC` | numeric | Serum cystatin C | mg/L | Must be > 0 |
| `UN` | numeric | Blood urea nitrogen | mg/dL | Must be > 0 |
| `Alb` | numeric | Serum albumin | g/dL | Must be > 0 |
| `Hb` | numeric | Hemoglobin | g/dL | — |

### Optional Columns

| Column | Data Type | Description | Unit | Notes |
|--------|-----------|-------------|------|-------|
| `HT` | numeric | Height | cm or m | Auto-converted if ≤ 3 (treated as meters) |
| `BW` | numeric | Body weight | kg | — |

HT and BW are used for:
- BMI computation (subgroup analyses)
- eGFRindiv calculation (Du Bois BSA formula)
- Anthropometric outlier exclusion

### Anthropometric Exclusion Criteria

Rows are excluded if:
- Height < 100 cm or > 220 cm
- Weight < 30 kg or > 200 kg

### Example Row

```csv
Date,Age,Sex,Cre,CysC,UN,Alb,Hb,HT,BW
2023-05-12,72,1,0.85,1.20,18.4,3.5,11.2,163,58.0
2023-05-13,65,0,0.62,0.91,12.1,4.1,12.8,155,52.3
```

### Notes on Missing Data

- Rows with missing values in required columns (`Date`, `Age`, `Sex`, `Cre`, `CysC`, `UN`, `Alb`, `Hb`) are excluded from the implementable model analysis.
- Rows with missing `HT` or `BW` are retained but excluded from BMI subgroup analyses and eGFRindiv calculations.
- One record per patient visit is expected; the pipeline does not deduplicate by patient ID.

### Data Privacy

Patient data from Gifu University Hospital cannot be shared publicly due to institutional and ethical restrictions. De-identified data may be available from the corresponding author upon reasonable request and with approval from the Ethics Committee of Gifu University Graduate School of Medicine.
