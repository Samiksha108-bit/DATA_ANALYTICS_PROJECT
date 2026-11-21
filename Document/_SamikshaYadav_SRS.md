# Software Requirements Specification (SRS)

**Project Title:** Hospital Patient Admission and Length of Stay Analysis
**Domain:** Healthcare: Hospital Admission Data Analysis

*Data Analyst Training Project, B.Tech / BCA First Year*

---

## Student Details

| Field | Details |
|---|---|
| **Name** | Samiksha Yadav |
| **Admission No** | 2026B01010556 |
| **Email ID** | samiksha.26b01010556@abes.ac.in |
| **Branch / Section** | CSE / CSE-12 |
| **GitHub Link** | [https://github.com/Samiksha108-bit/DATA_ANALYTICS_PROJECT](https://github.com/Samiksha108-bit/DATA_ANALYTICS_PROJECT) |
| **Date** | 21-11-2026 |

---

## 1. Introduction

### 1.1 Purpose

This document describes what the Hospital Patient Admission and Length of Stay Analysis project must do, which data it uses, and when it is considered complete. It is written for the course instructor and evaluators who review the project, and for any hospital manager who wants to understand how the analysis was planned.

### 1.2 Project Scope

**In scope**

- Loading and inspecting the hospital dataset (`hospital_data.csv`)
- Cleaning the data: duplicates, missing values, inconsistent city names and impossible ages
- Creating new columns such as `Age_Group`, `Comorbidity_Level` and `Waiting_Category`
- Answering four business questions with tables and charts
- Writing insights and recommendations for hospital management

**Out of scope**

- Predicting future admissions or length of stay (no machine learning)
- Live or real-time hospital data
- Building a website, dashboard or mobile app
- Medical diagnosis or treatment advice

### 1.3 Definitions

| Term | Meaning |
|---|---|
| SRS | Software Requirements Specification |
| CSV | Comma-separated values file |
| EDA | Exploratory data analysis |
| Length of Stay (LOS) | Number of days a patient stays in the hospital, from admission to discharge |
| Comorbidity | An additional medical condition a patient has besides the main reason for admission |
| Systolic BP | The upper blood pressure reading, measured in mmHg |
| Admission Type | How the patient came to the hospital: Emergency, Planned (booked in advance) or Referral (sent by another doctor or hospital) |
| Waiting Time | Minutes a patient waits before being attended to |

### 1.4 References

- Sample project: ABES_DA_Student_Performance_Project
- Training notebooks, Day 1 to Day 5
- pandas documentation: https://pandas.pydata.org/docs/
- Matplotlib and Seaborn documentation: https://matplotlib.org and https://seaborn.pydata.org

---

## 2. Overall Description

### 2.1 Business Problem

A hospital admits patients to five departments from several cities, through emergency, planned and referral routes. Beds, doctors and staff are limited, but the management does not clearly know which departments keep patients for the longest time, which patients wait the longest before being attended to, or how patient age and existing conditions affect the length of stay. This analysis will help the manager plan beds and staff for each department, reduce waiting time, and identify the patient groups that need extra attention.

### 2.2 Objectives

The project answers these four questions:

1. Which department has the longest average length of stay, and how many patients does each department handle?
2. How does the admission type (Emergency, Planned, Referral) affect waiting time and length of stay?
3. How do patient age group and number of comorbidities relate to length of stay?
4. Which cities send the most patients, and in which city is the average waiting time the longest?

### 2.3 Intended Users

| User | What they need from this project |
|---|---|
| Hospital administrator | To see overall admission volume and which cities and departments bring in the most patients |
| Bed / capacity planner | To know which departments have the longest stays, so beds can be planned in advance |
| Emergency and OPD manager | To find where and for which admission type waiting time is highest |
| Department head | To understand how age and comorbidities affect the stay of patients in the department |
| Course instructor / evaluator | To check the data cleaning, analysis and insights for correctness |

### 2.4 Tools and Environment

| Tool | Version | Used for |
|---|---|---|
| Python | 3.12 | Programming language |
| pandas | 3.0 | Loading, cleaning and grouping data |
| NumPy | 2.x | Calculations on numeric columns |
| Matplotlib / Seaborn | 3.11 / 0.13 | Charts |
| Jupyter Notebook / VS Code | Latest | Writing and running the notebook |
| GitHub | - | Storing and sharing the project |

### 2.5 Assumptions and Constraints

- The dataset is synthetic and was created with an AI tool. It does not contain real patient records.
- The dataset has 255 rows (250 unique patients), so the results show patterns in this data only.
- Valid ranges used for cleaning: Age 0 to 100 years, `Systolic_BP` 70 to 250 mmHg, `Waiting_Time_Mins` 0 or more, `Length_of_Stay_Days` 1 or more.
- Admission dates run from 19-11-2024 to 17-11-2026 and none is later than the date of this document.
- Gender value "Other" (2 rows) is treated as a valid category and is kept.

---

## 3. Data Requirements

### 3.1 Dataset Description

| Item | Value |
|---|---|
| **File name** | `hospital_data.csv` |
| **Location** | `data/raw` |
| **Number of rows** | 255 (250 unique patients after removing 5 duplicates) |
| **Number of columns** | 11 |
| **Outcome column** | `Length_of_Stay_Days` (the number of days a patient stays in the hospital) |
| **Source** | Synthetic data created with an AI tool |

### 3.2 Data Dictionary

Every column of the raw dataset is listed below.

| Column | Type | Valid values or range | Meaning |
|---|---|---|---|
| `Patient_ID` | ID | P1001 to P1250, unique per patient | Unique identifier of the patient |
| `Age` | Number | 0 to 100 years | Age of the patient in years |
| `Gender` | Category | Male, Female, Other | Gender of the patient |
| `City` | Category | Delhi, Lucknow, Mumbai, Ghaziabad, Pune | City the patient comes from |
| `Department` | Category | General Medicine, Cardiology, Orthopedics, Surgery, Pediatrics | Hospital department where the patient is admitted |
| `Admission_Type` | Category | Emergency, Planned, Referral | How the patient came to the hospital |
| `Num_Comorbidities` | Number | 0 to 5 | Number of other medical conditions the patient has |
| `Systolic_BP` | Number | 70 to 250 mmHg (data: 90 to 163) | Systolic blood pressure at admission |
| `Waiting_Time_Mins` | Number | 0 or more (data: 5 to 236) | Minutes the patient waited before being attended to |
| `Admission_Date` | Date | YYYY-MM-DD | Date on which the patient was admitted |
| `Length_of_Stay_Days` | Number | 1 to 12 | **Outcome:** number of days the patient stayed in the hospital |

### 3.3 Data Quality Issues

These problems were found in the first look at `hospital_data.csv`.

| Column | Problem found | How many | How it will be fixed, and why |
|---|---|---|---|
| Patient_ID (all columns) | Duplicate rows | 5 | Remove them and keep the first copy. Five patients (P1026, P1031, P1153, P1176, P1200) appear twice with identical values, and keeping both would count the same admission twice. |
| `Age` | Missing values | 5 | Fill with the median age of valid rows (47 years), because the median is not pulled by extreme ages. |
| `Age` | Impossible values | 2 | Values of -34 and 214 cannot be real ages. Mark them as missing and fill with the median, because the true age is unknown. |
| `City` | Inconsistent text | 7 | Remove extra spaces and make the capitalisation the same (for example "mumbai", " DELHI", "Lucknow  " become Mumbai, Delhi, Lucknow), so each city is counted as one group. |
| `Admission_Type` | Missing values | 5 | Fill with "Unknown". Admission type is a category, and guessing it could mislead the comparison between types. |
| `Systolic_BP` | Missing values | 6 | Fill with the median (about 127 mmHg), because it is robust to unusually high or low readings. |
| `Waiting_Time_Mins` | Missing values | 4 | Fill with the median (58 minutes), because waiting time is right-skewed and a few long waits would distort the mean. |

---

## 4. Functional Requirements

Functional requirements say what the project must do.

| ID | Requirement | Details for my project |
|---|---|---|
| FR-1 | Load the dataset | Read `data/raw/hospital_data.csv` into a pandas DataFrame using `pd.read_csv()`. |
| FR-2 | Inspect the data | Check the shape (255 rows, 11 columns), data types, missing values per column, duplicate rows, unique values of each category column and the min / max of each numeric column. |
| FR-3 | Clean the data | Remove 5 duplicate rows; strip spaces and standardise the case of `City`; replace impossible ages (below 0 or above 100) with missing; fill missing `Age`, `Systolic_BP` and `Waiting_Time_Mins` with the median; fill missing `Admission_Type` with "Unknown"; convert `Admission_Date` to datetime. |
| FR-4 | Create new columns | `Age_Group` (Child 0 to 17, Adult 18 to 59, Senior 60+); `Comorbidity_Level` (None = 0, Low = 1 to 2, High = 3 or more); `Waiting_Category` (Short up to 30 min, Medium 31 to 60 min, Long above 60 min); `Admission_Month` (year and month taken from `Admission_Date`); `Long_Stay` (Yes if `Length_of_Stay_Days` is 8 or more, otherwise No). |
| FR-5 | Answer the four questions | Q1: group by `Department`, count patients and take the mean of `Length_of_Stay_Days`. Q2: group by `Admission_Type`, compare mean `Waiting_Time_Mins` and mean `Length_of_Stay_Days`. Q3: group by `Age_Group` and `Comorbidity_Level` and compare mean `Length_of_Stay_Days`. Q4: count patients by `City` and compare mean `Waiting_Time_Mins` by `City`. |
| FR-6 | Visualise the results | Q1: bar chart of average stay by department. Q2: grouped bar chart or box plot of waiting time and stay by admission type. Q3: bar chart of average stay by age group, and a line or bar chart by number of comorbidities. Q4: bar chart of patient count and of average waiting time by city. At least four charts in total. |
| FR-7 | Save the outputs | Save the cleaned file as `data/processed/hospital_data_cleaned.csv` and save every chart as a PNG file in the `images` folder. |

---

## 5. Non-Functional Requirements

Non-functional requirements say how well the project must work.

| ID | Requirement | How my project meets it |
|---|---|---|
| NFR-1 | Reproducible | The notebook runs from top to bottom without errors, reading the raw file from `data/raw` every time. |
| NFR-2 | Readable | Every step has a short explanation in a markdown cell; variable and column names are meaningful. |
| NFR-3 | Accurate | Numbers in the README and slides match the notebook output. |
| NFR-4 | Well organised | Files are kept in the correct folders: `data/raw`, `data/processed`, `notebooks`, `images` and `documents`. |
| NFR-5 | No personal data | The dataset has no names, phone numbers or e-mail addresses. Patients are identified only by an anonymous `Patient_ID`. |

---

## 6. Project Workflow

The flow of the project, one line per step.

| Step | Stage | What happens in my project |
|---|---|---|
| 1 | Raw data | `hospital_data.csv` (255 rows, 11 columns) is stored in `data/raw`. |
| 2 | Inspect | Size, data types, blanks and duplicates are checked. |
| 3 | Clean | The problems from section 3.3 are fixed. |
| 4 | Transform | `Age_Group`, `Comorbidity_Level`, `Waiting_Category`, `Admission_Month` and `Long_Stay` are created. |
| 5 | Analyse | Tables and charts answer the four questions. |
| 6 | Insights | Findings and recommendations are written in the README and the presentation. |

**Project folder structure**

```text
DATA_ANALYTICS_PROJECT/
├── data/
│   ├── raw/          hospital_data.csv (original file)
│   └── processed/    hospital_data_cleaned.csv
├── notebooks/        first-look and analysis notebooks
├── images/           chart images (PNG)
├── documents/        SRS document and presentation
└── README.md         problem, questions, findings and charts
```

---

## 7. Deliverables and Timeline

| Deliverable | Where it is | Completed after |
|---|---|---|
| Raw dataset | `data/raw` | Day 3 |
| First-look notebook | `notebooks` | Day 3 |
| Analysis notebook | `notebooks` | Day 4 and Day 5 |
| Cleaned dataset | `data/processed` | Day 5 |
| At least four charts | `images` | Day 5 |
| README with findings | Main folder | Day 5 |
| GitHub repository | [DATA_ANALYTICS_PROJECT](https://github.com/Samiksha108-bit/DATA_ANALYTICS_PROJECT) | Day 5 |
| SRS document (this file) | Submitted separately | 21-11-2026 |
| Project presentation | Submitted separately | As per instructor schedule |

---

## 8. Acceptance Criteria

The project is complete when all of these are true:

- [ ] The notebook runs from top to bottom without errors.
- [ ] All data quality issues in section 3.3 are fixed and explained.
- [ ] At least three new columns are created (this project creates five).
- [ ] Each of the four questions is answered with a table or a chart.
- [ ] At least four charts are saved in the `images` folder.
- [ ] The README shows the business problem, questions, findings and charts.
- [ ] The GitHub link opens without signing in.

---

## 9. Declaration

I declare that the cleaning, analysis, charts and insights in this project are my own work. Only the dataset was created with an AI tool.

| Field | Details |
|---|---|
| **Name** | Samiksha Yadav |
| **Admission No** | 2026B01010556 |
| **Email ID** | samiksha.26b01010556@abes.ac.in |
| **Date** | 21-11-2026 |
