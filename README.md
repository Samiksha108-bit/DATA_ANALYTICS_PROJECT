# Hospital Admissions Dataset

A small, synthetic-style dataset of hospital patient admissions across several Indian cities. It is suited to exploratory data analysis, data-cleaning practice, and simple modelling of **length of stay** and **waiting time**.

- **File:** `hospital_data.csv`
- **Rows:** 255 records (250 unique `Patient_ID`s after removing duplicates)
- **Columns:** 11
- **Admission period:** 2024-11-19 to 2026-11-17

## Column Dictionary

| Column | Type | Description | Notes |
|---|---|---|---|
| `Patient_ID` | string | Patient identifier (e.g. `P1050`) | 5 duplicated IDs |
| `Age` | float | Patient age in years | 5 missing; contains invalid values (see below) |
| `Gender` | string | `Female`, `Male`, `Other` | 127 / 126 / 2 |
| `City` | string | City of the patient / hospital | Inconsistent casing and whitespace |
| `Department` | string | Admitting department | 5 categories |
| `Admission_Type` | string | `Emergency`, `Planned`, `Referral` | 5 missing |
| `Num_Comorbidities` | integer | Count of existing conditions (0–5) | Complete |
| `Systolic_BP` | float | Systolic blood pressure in mmHg | 6 missing; range 90–163 |
| `Waiting_Time_Mins` | float | Wait before admission/treatment, in minutes | 4 missing; range 5–236 |
| `Admission_Date` | date | Admission date (`YYYY-MM-DD`) | Complete |
| `Length_of_Stay_Days` | integer | Days stayed in hospital (1–12) | Complete; natural target variable |

## Category Values

- **Department:** General Medicine (78), Cardiology (65), Orthopedics (53), Surgery (33), Pediatrics (26)
- **Admission_Type:** Emergency (132), Planned (78), Referral (40), missing (5)
- **City (after normalization):** Delhi, Lucknow, Mumbai, Ghaziabad, Pune

## Summary Statistics

| Metric | Age | Systolic BP | Waiting Time (mins) | Length of Stay (days) |
|---|---|---|---|---|
| Mean | 45.8 | 127.1 | 65.3 | 5.7 |
| Median | 47 | 128 | 58 | 6 |
| Min | -34 | 90 | 5 | 1 |
| Max | 214 | 163 | 236 | 12 |

Figures are computed on the raw file, so they include the invalid age values noted below.

## Known Data Quality Issues

This file is deliberately "messy" and needs cleaning before analysis.

1. **Duplicate records:** 5 fully identical rows (`P1026`, `P1031`, `P1153`, `P1176`, `P1200`). Drop with `df.drop_duplicates()`.
2. **Inconsistent city names:** e.g. `mumbai`, ` DELHI`, `Lucknow  `, `GHAZIABAD`. Normalize with `str.strip().str.title()`.
3. **Impossible ages:** one negative value (-34) and one implausible value (214). Treat as errors and set to missing or correct.
4. **Missing values:** `Age` (5), `Admission_Type` (5), `Systolic_BP` (6), `Waiting_Time_Mins` (4).
5. **Waiting-time outliers:** 3 records exceed 180 minutes. Verify whether they are genuine.
6. **Future-dated admissions:** the date range extends to 2026-11-17, so check whether this is intended.
7. **Rare category:** `Gender = Other` has only 2 records, which is too few for reliable group comparisons.

## Quick Start

```python
import pandas as pd

df = pd.read_csv("hospital_data.csv", parse_dates=["Admission_Date"])

# Basic cleaning
df = df.drop_duplicates()
df["City"] = df["City"].str.strip().str.title()
df.loc[(df["Age"] < 0) | (df["Age"] > 110), "Age"] = pd.NA

print(df.shape)
print(df.isna().sum())
```

## Example Analyses

- Average length of stay by department or admission type
- Relationship between waiting time and admission type
- Effect of age and number of comorbidities on length of stay
- Monthly admission trends by city
- Predicting length of stay with regression models

## Notes and Limitations

- The dataset is small (about 250 unique patients), so results are illustrative rather than statistically robust.
- No personally identifying information beyond `Patient_ID` is included.
- It is not suitable for clinical decision-making.
