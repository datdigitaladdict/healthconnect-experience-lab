# HealthConnect Experience Lab
### AnalystLab Africa Data Science Internship - Data Science Track

This repository documents the Data Science track's contribution to the
**HealthConnect Clinic Experience Lab**, a shared, multi-track project
undertaken by AnalystLab Africa interns from Week 4 onward.

## Project Title
**Improving Patient Appointment Attendance and Healthcare Support Using
Data and AI**

## Business Scenario

HealthConnect Clinic is a fictional healthcare provider facing several
operational challenges:

- Patients missing scheduled appointments (no-shows)
- Difficulty understanding what drives no-show behavior
- Inefficient use of appointment slots when patients fail to attend
- Repetitive patient enquiries about appointments and procedures
- A need to improve patient engagement and administrative support

**Central Project Question:** How can HealthConnect Clinic use data and
AI to reduce missed appointments and improve the patient support
experience?

Multiple internship tracks (Project Management, Data Analytics, Data
Science, Machine Learning Engineering, and Generative AI) are
contributing to this shared project, each from their own professional
perspective. This repository covers the **Data Science track's** work.

## Data Science Track Role

The Data Science track is responsible for defining the machine learning
problem: assessing whether the available appointment data can support a
no-show prediction solution, and laying the groundwork for model
development in later weeks.

## Project Stages

```
Problem Understanding  →  Analysis & Solution Design  →  Development
        ↓                                                    ↓
                    Testing & Refinement  →  Final Presentation
```

This repository will be updated incrementally as the project progresses
through each stage.

## Week 4: Problem Understanding & ML Problem Definition

Week 4 focused on understanding the business problem and defining the
machine learning approach, without building or deploying a model.

### Dataset

**File:** `HealthConnect_Appointment_Data.csv`
5,000 appointment records, 18 variables, including patient
demographics, appointment details, booking information, appointment
history, reminder information, distance to clinic, and appointment
outcome.

### Key Findings

| Check | Result |
|---|---|
| Rows | 5,000 |
| Duplicate records | 0 |
| Missing `reminder_channel` | 1,366 rows (structurally expected — occurs only when no reminder was sent) |
| Missing `distance_to_clinic_km` | 90 rows |
| Missing `waiting_time_minutes` | 60 rows |
| Target distribution | No-Show 48.5%, Attended 46.3%, Cancelled 5.3% |
| Unique patients | 1,696 (across 5,000 appointments) |

### Machine Learning Problem Definition

- **Problem type:** Binary classification
- **Target variable:** `appointment_outcome`, recoded as
  `target_no_show` (1 = No-Show, 0 = Attended)
- **Handling of cancellations:** Cancelled appointments (263 rows,
  5.3%) are excluded from the modelling dataset, since a cancellation
  is a distinct, proactive patient behavior rather than a silent
  no-show. This leaves 4,737 rows for modelling (51.2% No-Show, 48.8%
  Attended).
- **Potential features:** patient demographics, appointment
  characteristics, booking lead time, patient appointment history
  (including an engineered prior no-show rate), and reminder
  information.
- **Key modelling consideration:** `waiting_time_minutes` was flagged
  as a likely data leakage risk, since it is probably only known after
  a patient has already attended, and has been excluded from the
  proposed feature set pending confirmation.

Full details, including the data quality assessment, feature
justification, initial modelling approach, and assumptions/limitations/
risks, are documented in the Week 4 notebook.

**Notebook:** [`Week_4/notebooks/AnalystLab_Africa_Week4_HealthConnect.ipynb`](./Week_4/notebooks/AnalystLab_Africa_Week4_HealthConnect.ipynb)

## Repository Structure

```
healthconnect-experience-lab/
│
├── README.md
├── requirements.txt
├── notebooks/
    │   └── AnalystLab_Africa_Week4_HealthConnect.ipynb
├── data/
    │   └── HealthConnect_Appointment_Data.csv
        └── HealthConnect_Data_Dictionary - Data Dictionary.csv 
└── reports/
    │
        └── Week_4_Project_Summary
```

## Technologies and Libraries

- Python, Jupyter Notebook
- Pandas, NumPy
- Matplotlib, Seaborn (for future EDA)
- Scikit-learn (for future modelling)

## Proposed Focus for Week 5

Begin exploratory data analysis on the finalized modelling dataset
(No-Show vs Attended), validate proposed features against the target
variable, confirm the treatment of `waiting_time_minutes`, and build an
initial baseline model.

## Author

**Onyinyechi Osuji**

Data Science Intern

AnalystLab Africa Experience Lab - HealthConnect Project

#AnalystLabAfrica

## Week 5: Data Preparation, Feature Engineering & Baseline Model

Week 5 moved from problem definition into practical implementation:
preparing the modelling dataset, engineering new features, and
building an initial baseline classification model.

### Data Preparation

- Confirmed the Week 4 target variable and cancellation-handling
  decision (unchanged).
- Corrected the reasoning behind excluding `waiting_time_minutes`:
  empirical testing showed it has virtually identical distributions
  across No-Show and Attended groups, so it is a non-informative
  feature rather than a data leakage risk as originally assumed.
- Handled missing values (`reminder_channel` → "None" category,
  `distance_to_clinic_km` → median imputation) and one-hot encoded all
  categorical variables.

### Key EDA Findings

| Relationship | Finding |
|---|---|
| Booking lead time vs no-show | No-show rate rises from 29.5% (short lead time) to 63.9% (long lead time) — the strongest pattern found |
| Reminder sent vs no-show | 49.9% no-show rate with a reminder vs 54.6% without |
| Prior no-shows vs no-show | Mean prior no-show rate of 0.191 for No-Show vs 0.146 for Attended |

### Engineered Features

- `prior_no_show_rate` — previous no-shows as a proportion of previous appointments
- `booking_lead_category` — Short/Medium/Long grouping of booking lead time
- `has_prior_history` — flags first-time vs returning patients

### Train/Test Strategy

A **patient-aware** split (`GroupShuffleSplit`, grouped by `patient_id`)
was used instead of a standard row-level split, since many patients
appear multiple times in the dataset. Verified zero patient overlap
between the training and test sets.

### Baseline Model & Results

**Model:** Logistic Regression (chosen for interpretability as a first
benchmark)

| Metric | Score |
|---|---|
| Accuracy | 0.631 |
| Precision (No-Show) | 0.62 |
| Recall (No-Show) | 0.66 |
| F1-score (No-Show) | 0.64 |
| ROC-AUC | 0.677 |

The baseline performs meaningfully better than random guessing and
correctly identifies about two-thirds of genuine no-shows, providing a
usable first benchmark for HealthConnect Clinic.

**Notebook:** [`Week_5/notebooks/AnalystLab_Africa_week5_healthconnect.ipynb`](./Week_5/notebooks/AnalystLab_Africa_week5_healthconnect.ipynb)
**Project Summary:** [`Week_5/reports/Week_5_Project_Summary.pdf`](./Week_5/reports/Week_5_Project_Summary.pdf)

### Proposed Focus for Week 6

Test tree-based models (Random Forest, Gradient Boosting) against the
Logistic Regression baseline, perform hyperparameter tuning, examine
feature importance, and consider classification threshold adjustments.
