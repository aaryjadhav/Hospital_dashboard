# 🏥 Hospital Analytics Dashboard | Power BI
<img width="1920" height="1080" alt="Blue and White Modern Hospital Services Medical Presentation" src="https://github.com/user-attachments/assets/b3ebe625-c965-4250-b8bf-52165903c31b" />

## 🔍 Overview

An end-to-end **Hospital Analytics Dashboard** developed to help healthcare management make data-driven decisions. This project consolidates operational, financial, and patient-related data into a single, interactive analytical view, showcasing expertise in data modeling, DAX, and business-oriented storytelling.

## 🎯 Business Problem

Hospital leadership often struggles to gain a "birds-eye view" because data is siloed across departments (Pharmacy, Billing, Front Desk, etc.). Key challenges include:

* **Performance Tracking:** Difficulty monitoring real-time KPIs.
* **Bottlenecks:** Identifying delays in patient discharge or bed turnover.
* **Financial Visibility:** Tracking revenue leakage and pharmacy profitability.
* **Quality Control:** Measuring doctor performance and patient satisfaction.

## 🧠 Solution Approach

* **Scalable Architecture:** Designed a robust **Star Schema** data model for optimized performance.
* **Custom Business Logic:** Authored complex DAX measures to calculate operational metrics.
* **User-Centric Design:** Built 6 intuitive pages tailored to different hospital stakeholders.
* **Time Intelligence:** Integrated a dedicated Calendar table for trend analysis (YoY, MoM).

---

## 📊 Dashboard Pages

The report is structured into 6 focused modules:

1. **Home:** Executive snapshot and intuitive navigation menu.
2. **Overview:** High-level KPIs (Total Revenue, Total Admissions, Occupancy Rate).
3. **Patient Analytics:** Trends in admissions, demographics, and discharge status.
4. **Doctor Analytics:** Performance tracking, ratings, and commission structures.
5. **Hospital Operations:** Real-time bed occupancy and room utilization.
6. **Finance & Pharmacy:** Billing trends, payment methods, and medicine sales/profit.

---

## 🧩 Data Modeling & Logic

### Data Schema
I utilized a **Star Schema** to ensure fast filtering and simple DAX maintenance.
* **Fact Tables:** `Appointments`, `Hospital Bills`, `Medicine Sales`, `Patient Tests`.
* **Dimension Tables:** `Patient`, `Doctor`, `Department`, `Medical Stock`, `Calendar`.

### Key DAX Metrics
* **Occupancy Rate:** Total occupied beds vs. total capacity.
* **Pharmacy Profitability:** `(Sales Price - Cost Price) * Quantity`.
* **Doctor Commission:** Dynamic calculations based on billing and department types.
* **Revenue Growth:** Year-over-Year (YoY) financial performance tracking.

## 🛠 Tech Stack
* **Power BI Desktop:** Dashboard authoring and visualization.
* **Power Query:** Data cleaning and ETL (Extract, Transform, Load).
* **DAX:** Advanced analytical calculations.
* **MySQL:** Underlying data source management.

## 📈 Key Learnings
* **Data Integrity:** Healthcare analytics requires 100% accuracy; cleaning nulls in patient records is vital.
* **Actionable Insights:** A dashboard shouldn't just show data; it should highlight *where* management needs to act.
* **Scalability:** Designing the model properly from day one allows for easy addition of new departments later.

---

## 📂 Repository Structure

```
├── Excel Files - Direct Import/               # Raw datasets used for the project
├── MySQL Database/     # SQL script for database schema/data
├── Dashboard/          # The .pbix Power BI file
├── Images/             # Dashboard screenshots, backgrounds, and icons
└── README.md           # Project documentation
```
## 👤 Author
**Aary Jadhav** *Final-Year Engineering Student – Artificial Intelligence & Machine Learning* 📍 Seeking opportunities in **Data Analytics / Business Intelligence**

---

**Project walkthrough, dashboard demo, and DAX explanation available on request.**

---

## Set of DAX Formulas

## 1️⃣ Patient Analytics DAX

### Total Patients

```DAX
Total_Patients =
DISTINCTCOUNT(Patient[patient_id])
```

**Logic:**
Counts unique patients to avoid duplication across visits.

---

### Admitted Patients

```DAX
Total_Admitted_Patient =
CALCULATE(
    DISTINCTCOUNT(Patient[patient_id]),
    Patient[status] = "Admitted"
)
```

**Business Logic:**
Helps management track current hospital load.

---

### Discharged Patients

```DAX
Total_Discharged_Patient =
CALCULATE(
    DISTINCTCOUNT(Patient[patient_id]),
    Patient[status] = "Discharged"
)
```

**Reason:**
Used to compare patient inflow vs outflow.

---

### Patient Age Category

```DAX
Age_Category =
SWITCH(
    TRUE(),
    Patient[age] <= 30, "18–30",
    Patient[age] <= 45, "31–45",
    Patient[age] <= 60, "46–60",
    "60+"
)
```

**Logic:**
Enables demographic-based analysis and healthcare planning.

---

## 2️⃣ Calendar / Time Intelligence

### Calendar Table

```DAX
Calendar =
ADDCOLUMNS(
    CALENDAR(DATE(2023,1,1), DATE(2023,12,31)),
    "Month", FORMAT([Date], "mmm"),
    "Month-Year", FORMAT([Date], "mmm-yy"),
    "Day", FORMAT([Date], "ddd")
)
```

**Why Important:**
Enables month-wise and day-wise trend analysis, especially for:

* Patient admissions
* Medicine sales

---

## 3️⃣ Hospital Finance DAX

### Paid Amount

```DAX
Paid_Amount =
CALCULATE(
    SUM(Hospital_Bills[Value]),
    Hospital_Bills[Charges Type] = "Paid Amount"
)
```

**Logic:**
Ensures only actual paid revenue is counted.

---

### Revenue by Payment Mode

```DAX
Cash_Pay =
CALCULATE([Paid_Amount], Hospital_Bills[payment_method] = "Cash")

Card_Pay =
CALCULATE([Paid_Amount], Hospital_Bills[payment_method] = "Credit Card")

Insurance_Pay =
CALCULATE([Paid_Amount], Hospital_Bills[payment_method] = "Insurance")
```

**Business Use:**
Helps finance teams understand revenue mix and cash flow risk.

---

### Average Bill Value

```DAX
Avg_Bill =
DIVIDE([Paid_Amount], DISTINCTCOUNT(Hospital_Bills[bill_id]))
```

**Logic:**
Prevents divide-by-zero errors and shows spending trends.

---

## 4️⃣ Doctor Performance & Commission

### Doctor Commission

```DAX
Doctor_Commission =
[Paid_Amount] * 0.10
```

**Reason:**
Commission is calculated dynamically based on hospital revenue.

---

### Total Doctor Salary

```DAX
Doctors_Salary =
SUM(Doctor[salary]) + [Doctor_Commission]
```

**Business Logic:**
Combines fixed salary with variable incentives.

---

## 5️⃣ Beds & Hospital Operations

### Available Beds

```DAX
Bed_Available =
CALCULATE(
    DISTINCTCOUNT(Beds[bed_id]),
    Beds[status] = "Available"
)
```

### Occupied Beds

```DAX
Bed_Occupied =
CALCULATE(
    DISTINCTCOUNT(Beds[bed_id]),
    Beds[status] = "Occupied"
)
```

**Operational Use:**
Supports capacity planning and emergency readiness.

---

## 6️⃣ Pharmacy & Medical Stock

### Medicine Sales Quantity

```DAX
Sales_Med_QTY =
SUM(Medicine_Sales[qty])
```

### Total Stock

```DAX
Total_Stock =
SUM(Medical_Stock[stock_qty]) + [Sales_Med_QTY]
```

### Total Stock Selling Price

```DAX
Total_Stock_Selling_Price =
SUM(Medical_Stock[unit_price]) * [Total_Stock]
```

### Total Stock Purchasing Price

```DAX
Total_Stock_Purchasing_Price =
SUM(Medical_Stock[cost_price]) * [Total_Stock]
```

### Pharmacy Profit

```DAX
Total_Profit =
[Total_Stock_Selling_Price] - [Total_Stock_Purchasing_Price]
```

**Business Logic:**
Tracks medicine profitability and inventory efficiency.

---

## 7️⃣ Patient Satisfaction & Ratings

### Star Rating Display

```DAX
Rating_Icon =
SWITCH(
    Satisfaction_Score[rating],
    1,"⭐",
    2,"⭐⭐",
    3,"⭐⭐⭐",
    4,"⭐⭐⭐⭐",
    5,"⭐⭐⭐⭐⭐"
)
```

**Reason:**
Improves dashboard readability for non-technical users.

---

## 8️⃣ UI / UX Enhancements (Icons)

### Status Icon

```DAX
Status_Icon =
IF(
    Appointment[status] = "Completed",
    "https://i.postimg.cc/nLYJR0nj/double-check.png",
    "https://i.postimg.cc/Zqhmx1zz/calendar-7.png"
)
```

**Purpose:**
Visual cues for faster interpretation.

