# Fitness-Tracker-PowerBI
# 🏃‍♂️ Personal Fitness Tracker & Health Dashboard
### An Advanced Power BI Implementation for Health Metrics

This project is a high-performance **Power BI Dashboard** designed to function like a modern mobile fitness application. It tracks vital health metrics, calculates a proprietary "Health Score" based on user-defined targets, and provides a rolling 30-day analysis of physical activity.

---

## 🖥️ Dashboard Preview
![Fitness Tracker Dashboard]<img width="574" height="341" alt="image" src="https://github.com/user-attachments/assets/5d4623e4-5955-4c7f-8a81-b4ea7eb3d2fc" />


---

## 🚀 Core Features
* **Dynamic UI/UX:** Mobile-app style navigation with dark mode aesthetics and personlized greetings.
* **Health Score Algorithm:** A custom DAX-based scoring system that evaluates performance across Calories, Heart Rate, Steps, and Exercise Sessions.
* **Target vs. Actual Analysis:** Real-time tracking against Minimum and Maximum healthy ranges.
* **Base64 Image Integration:** Sophisticated DAX measures to render user profile images dynamically from partitioned binary data.
* **Rolling 30-Day Logic:** Automated calculation periods that adjust based on the "Today" reference or user selection.

---

## 📊 Key DAX Calculations
This dashboard utilizes advanced DAX for its "Brain." Here are some highlights:

### 1. The Health Score Logic
The score is calculated by finding the absolute deviation (Delta) from the midpoint of user targets across four categories, then normalizing it into a percentage:
```dax
Health Score = 
VAR AvgDeltaPct = ( CaloriesDeltaPct + HeartRateDeltaPct + StepsDeltaPct + ExerciseSessionsDeltaPct ) / 4
VAR Result = IF( ConditionShow , 1 - ( AvgDeltaPct / 2 ) )
RETURN Result
