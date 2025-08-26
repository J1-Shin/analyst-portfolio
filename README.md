# Healthcare No-show Analysis

## 📌 Project Overview
This project analyzes the **Medical Appointment No-shows** dataset from Brazil (`KaggleV2-May-2016.csv`).  
The goal is to explore patient appointment data, identify factors that influence no-show rates,  
and provide operational insights for improving scheduling efficiency.

- **Dataset**: [Kaggle - Medical Appointment No Shows](https://www.kaggle.com/datasets/joniarroba/noshowappointments)  
- **Key variables**:
  - `ScheduledDay`: The date when the appointment was booked
  - `AppointmentDay`: The actual appointment date
  - `No-show`: Whether the patient attended (`No`) or missed (`Yes`)
  - Other demographic and health-related variables: `Age`, `Gender`, `Scholarship`, `SMS_received`, etc.

---

## 🔑 Key Takeaways (TL;DR)
- **Weekday Effect**: No-shows spike on certain weekdays (e.g., Monday/Friday)  
- **Lead Time Effect**: Longer scheduling gaps → higher no-show risk  
- **Demographics**: Young adults (19–30) most likely to miss appointments; seniors least likely  
- **SMS Reminders**: Alone, not effective — likely targeted at high-risk groups  

---

## 🗂️ Analysis Steps

### 1. No-show by Weekday (`01_eda.ipynb`)
- Converted `AppointmentDay` into weekdays (Mon–Sun)
- Aggregated **total appointments, number of no-shows, and no-show rate**
- Visualized weekday-level no-show rate with bar chart

**Artifacts**
- `data/processed/weekday_noshow_summary.csv`
- `assets/no_show_rate_by_weekday.png`

**Preview**
![](assets/no_show_rate_by_weekday.png)

**Key Insight**
- No-show rates vary across weekdays (e.g., higher on certain weekdays such as Monday/Friday)  
- This suggests possible links with weekend/holiday patterns → **tailored reminder strategies per weekday**

---

### 2. No-show by Lead Time (`02_leadtime_analysis.ipynb`)
- Calculated `LeadTime = AppointmentDay - ScheduledDay`
- Cleaned data:
  - Removed negative/invalid values
  - Capped extreme values (>60 days) to stabilize distribution
- Grouped lead time into buckets:
  - `0d`, `1–2d`, `3–5d`, `6–10d`, `11–20d`, `21–30d`, `31–60d`
- Aggregated no-show statistics and visualized

**Artifacts**
- `data/processed/leadtime_noshow_summary.csv`
- `assets/no_show_rate_by_leadtime.png`

**Preview**
![](assets/no_show_rate_by_leadtime.png)

**Key Insight**
- **Shorter lead times are associated with lower no-show rates**, while longer lead times significantly increase the risk of no-shows.  
- Example: Same-day (`0d`) appointments show the lowest no-show rate, whereas 21+ day lead times show noticeably higher rates.  
- **Actionable Suggestion**: Increase reminder frequency for long-lead-time patients; consider slight overbooking for this segment.

---

### 3. No-show by Demographics (`03_demographic_analysis.ipynb`)
- Created **age groups**: Child (0–12), Teen (13–18), Young Adult (19–30), Adult (31–45), Middle Age (46–60), Senior (60+)
- Grouped by **AgeGroup × Gender**
- Aggregated appointments, no-shows, and no-show rate
- Visualized both line chart and grouped bar chart

**Artifacts**
- `data/processed/demographic_noshow_summary.csv`
- `assets/no_show_rate_by_age_gender.png`
- `assets/no_show_rate_by_age_gender_bar.png`

**Preview**
_Line Chart (trend by age group and gender)_  
![](assets/no_show_rate_by_age_gender.png)

_Grouped Bar Chart (direct male/female comparison)_  
![](assets/no_show_rate_by_age_gender_bar.png)

**Key Insight**
- **Teens (13–18) and Young Adults (19–30)** show the **highest no-show rates**.  
- Seniors (60+) show the **lowest no-show rate**.  
- Gender differences exist but are relatively small; males sometimes show slightly higher no-show rates.  
- **Action Suggestion**: For younger groups (19–30), targeted digital reminders or flexible scheduling could reduce no-shows.

---

### 4. SMS Reminder Effect (`04_sms_analysis.ipynb`)

We analyzed whether receiving an SMS reminder influences the no-show rate.

#### 4.1 Overall Effect
- Grouped by SMS_received (0 = No SMS, 1 = SMS Sent)
- Compared overall appointment volume, no-shows, and no-show rate

**Artifacts**
- `data/processed/sms_noshow_summary.csv`
- `assets/no_show_rate_by_sms.png`

**Preview**
![](assets/no_show_rate_by_sms.png)

**Key Insight**
- Patients who **received an SMS had a *higher* no-show rate (27.6%) compared to those who did not receive one (16.7%)**.  
- This counter-intuitive result suggests that SMS reminders may have been targeted toward patients already considered at higher risk of no-shows.  
- **Action Suggestion**: SMS alone may not be sufficient; consider multi-channel reminders (e.g., phone calls, mobile app push).

---

#### 4.2 SMS Effect by Age Group
- Grouped by AgeGroup × SMS_received
- Compared how SMS impacted no-show rates across different demographics

**Artifacts**
- `data/processed/age_sms_summary.csv`
- `assets/no_show_rate_by_age_sms.png`

**Preview**
![](assets/no_show_rate_by_age_sms.png)

**Key Insight**
- SMS reminders were **more frequently sent to younger groups** (e.g., Young Adults 19–30), who also naturally exhibit higher no-show rates.  
- Seniors (60+) showed low no-show rates regardless of SMS.  
- This indicates a **selection bias**: SMS was not randomly assigned but likely targeted at groups with historically higher no-show risk.  
- **Action Suggestion**: Instead of blanket SMS campaigns, hospitals could benefit from **age-tailored reminder strategies** (e.g., stronger digital reminders for young adults, phone call follow-ups for middle-aged groups).

---

### 5. Modeling Prep (`05_model_prep.ipynb`)
We prepared features and split the dataset for modeling.

**Feature Engineering**
- `LeadTime` (days) from `AppointmentDay - ScheduledDay` (cleaned: negative removed, >60 capped)
- `GenderNum` (F=0, M=1)
- `AppointmentWeekday` one-hot encoded (drop-first)
- Target: `NoShow` (Yes=1, No=0)

**Train/Test Split**
- 80/20 random split with `stratify=y` (keeps class ratio consistent)
- `random_state=42` for reproducibility

**Artifacts**
- `data/processed/train_model_prep.csv`
- `data/processed/test_model_prep.csv`

---

### 6. Logistic Regression (`06_logistic_regression.ipynb`)
We trained a logistic regression model to predict patient no-shows.

#### 6.1 Baseline Model (Unbalanced)
- **Accuracy**: ~0.71
- **Recall (NoShow=1)**: Near 0 (fails to detect actual no-shows)
- **Insight**: The model is biased towards predicting "Show" due to class imbalance (~72% show rate).

#### 6.2 With Class Weight Balanced
We retrained using `class_weight='balanced'` to address class imbalance.


- **Accuracy**: ~0.55 (lower, but expected)
- **Recall (NoShow=1)**: Improved significantly (detects more no-shows)
- **ROC-AUC**: ~0.58 (slightly better overall discrimination)
- **Confusion Matrix** shows a clear reduction in **False Negatives**.

**Key Insight**
- High accuracy on imbalanced data is misleading.
- Balancing the classes improves the model's **ability to detect no-shows**, which is critical for operational use.

**Artifacts**
- `assets/logistic_regression_roc.png`
- `assets/logistic_regression_coefficients.csv`(balanced)

---

## 📊 Next Steps

1. **Handle Class Imbalance More Effectively**
   - Apply **SMOTE (Synthetic Minority Oversampling Technique)** to generate synthetic no-show examples.
   - Compare oversampling vs. undersampling vs. class weighting.

2. **Try Tree-Based Models**
   - Use **RandomForestClassifier** and **XGBoost** to capture nonlinear relationships.
   - Evaluate feature importances to understand complex interactions.

3. **Hyperparameter Tuning**
   - Perform cross-validation and grid search to optimize parameters for Logistic Regression and tree models.

4. **Enhanced Feature Engineering**
   - Add interaction features (e.g., `LeadTime x Weekday`, `SMS_received x AgeGroup`).
   - Consider time-of-day, previous visits, or geographic distance if available.

5. **Evaluate with Robust Metrics**
   - Focus on **Recall (NoShow=1)** and **Precision-Recall AUC** to better measure effectiveness in no-show detection.
   - Cost-sensitive evaluation: weigh the cost of missing a no-show vs. false alarms.

6. **Deployment Consideration**
   - Prepare a pipeline to retrain and update the model regularly as new data arrives.
   - Consider integration into hospital scheduling systems for real-time decision support.


---

## ⚙️ Environment
- Python 3.12  
- Libraries: pandas, numpy, matplotlib, jupyter  
- Virtual environment: `.venv`
---

## 📁 Project Structure
healthcare-no_show/
│
├── data/
│   ├── raw/                <- Original dataset (Kaggle CSV)
│   ├── processed/          <- Processed summaries (weekday, leadtime, demographic, sms)
│   │   ├── weekday_noshow_summary.csv
│   │   ├── leadtime_noshow_summary.csv
│   │   ├── demographic_noshow_summary.csv
│   │   ├── sms_noshow_summary.csv
│   │   ├── age_sms_summary.csv
│   │   ├── train_model_prep.csv
│   │   └── test_model_prep.csv
├── notebooks/
│   ├── 01_eda.ipynb
│   ├── 02_leadtime_analysis.ipynb
│   ├── 03_demographic_analysis.ipynb
│   ├── 04_sms_analysis.ipynb
│   └── 05_model_prep.ipynb
│
├── assets/                 <- Visualization outputs
│   ├── no_show_rate_by_weekday.png
│   ├── no_show_rate_by_leadtime.png
│   ├── no_show_rate_by_age_gender.png
│   ├── no_show_rate_by_age_gender_bar.png
│   ├── no_show_rate_by_sms.png
│   ├── no_show_rate_by_age_sms.png
│
└── README.md
