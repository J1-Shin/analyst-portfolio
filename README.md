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

## 📊 Next Steps
- Analyze no-show rates by **age group** and **gender**
- Assess the impact of **SMS reminders** on attendance
- Build a **cross-analysis (Weekday × Lead Time)** for deeper operational insights
- Summarize findings into a **dashboard-style report with KPIs**

---

## ⚙️ Environment
- Python 3.12  
- Libraries: pandas, numpy, matplotlib, jupyter  
- Virtual environment: `.venv` (see `requirements.txt`)  

---

## 📁 Project Structure
healthcare-no_show/
│
├── data/
│ ├── raw/ <- Original dataset (Kaggle CSV)
│ ├── processed/ <- Processed summaries
│
├── notebooks/
│ ├── 01_eda.ipynb
│ ├── 02_leadtime_analysis.ipynb
│
├── assets/ <- Visualization outputs
│ ├── no_show_rate_by_weekday.png
│ ├── no_show_rate_by_leadtime.png
│
└── README.md
