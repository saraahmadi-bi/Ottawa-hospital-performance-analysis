# DAX Measures

This document contains the main DAX measures used in the **Hospital Performance Analysis** Power BI report. The measures support the Executive Summary, Department Details, and Patient Analysis pages.

> **Note:** Measure and column names reflect the data model used in this project.

## Visit Measures

### Total Visits

Calculates the total number of unique hospital visits.

```DAX
Total Visits =
DISTINCTCOUNT(Visits[VisitID])
```

### Weekday Visits

Calculates visits that occurred on weekdays.

```DAX
Weekday Visits =
CALCULATE(
    [Total Visits],
    Visits[DayType] = "Weekday"
)
```

### Weekend Visits

Calculates visits that occurred on weekends.

```DAX
Weekend Visits =
CALCULATE(
    [Total Visits],
    Visits[DayType] = "Weekend"
)
```

### Weekend Visit Percentage

Shows the percentage of all visits that occurred on weekends.

```DAX
Weekend Visit % =
DIVIDE(
    [Weekend Visits],
    [Total Visits],
    0
)
```

### Average Visits per Patient

Calculates the average number of visits for each unique patient.

```DAX
Average Visits per Patient =
DIVIDE(
    [Total Visits],
    DISTINCTCOUNT(Visits[PatientID]),
    0
)
```

## Patient Measures

### Total Patients

Calculates the number of unique patients.

```DAX
Total Patients =
DISTINCTCOUNT(Patient[PatientID])
```

### Average Patient Age

Calculates the average age of patients.

```DAX
Average Patient Age =
AVERAGE(Patient[Age])
```

### Average Patient Rating

Calculates the average satisfaction rating recorded for hospital visits.

```DAX
Average Patient Rating =
AVERAGE(Visits[PatientRating])
```

### Most Common Visit Type

Returns the visit type with the highest number of visits in the current filter context.

```DAX
Most Common Visit Type =
VAR VisitTypeSummary =
    SUMMARIZE(
        Visits,
        Visits[VisitType],
        "VisitCount", DISTINCTCOUNT(Visits[VisitID])
    )
VAR TopVisitType =
    TOPN(
        1,
        VisitTypeSummary,
        [VisitCount], DESC,
        Visits[VisitType], ASC
    )
RETURN
    CONCATENATEX(
        TopVisitType,
        Visits[VisitType],
        ", "
    )
```

## Operational Measures

### Average Wait Time

Calculates the average patient wait time in minutes.

```DAX
Average Wait Time =
AVERAGE(Visits[WaitTimeMinutes])
```

### Rush Hour

Returns the hour with the greatest number of visits in the current filter context.

```DAX
Rush Hour =
VAR HourSummary =
    SUMMARIZE(
        Visits,
        Visits[ArrivalHour],
        "VisitCount", DISTINCTCOUNT(Visits[VisitID])
    )
VAR TopHour =
    TOPN(
        1,
        HourSummary,
        [VisitCount], DESC,
        Visits[ArrivalHour], ASC
    )
RETURN
    CONCATENATEX(
        TopHour,
        FORMAT(Visits[ArrivalHour], "00") & ":00",
        ", "
    )
```

### No-Show and Cancellation Rate

Calculates the percentage of appointments with a No-show or Cancelled status.

```DAX
No-Show & Cancellation Rate =
DIVIDE(
    CALCULATE(
        DISTINCTCOUNT(Appointment[AppointmentID]),
        Appointment[AppointmentStatus] IN { "No-show", "Cancelled" }
    ),
    DISTINCTCOUNT(Appointment[AppointmentID]),
    0
)
```

## Financial Measures

### Gross Billed Revenue

Calculates the total gross amount billed by the hospital.

```DAX
Gross Billed Revenue =
SUM(Billing[GrossBillAmountCAD])
```

### Public Coverage Amount

Calculates the total amount covered through public insurance.

```DAX
Public Coverage Amount =
SUM(Billing[PublicCoverageAmountCAD])
```

### Private Insurance Amount

Calculates the total amount covered through private insurance.

```DAX
Private Insurance Amount =
SUM(Billing[PrivateInsuranceAmountCAD])
```

### Total Insurance Coverage

Combines public and private insurance coverage amounts.

```DAX
Total Insurance Coverage =
[Public Coverage Amount] + [Private Insurance Amount]
```

### Insurance Coverage Percentage

Calculates the percentage of gross billed revenue covered by public and private insurance.

```DAX
Insurance Coverage % =
DIVIDE(
    SUM(Billing[PublicCoverageAmountCAD])
        + SUM(Billing[PrivateInsuranceAmountCAD]),
    SUM(Billing[GrossBillAmountCAD]),
    0
)
```

## Formatting

Recommended Power BI formats:

| Measure | Format |
|---|---|
| Total Visits | Whole number |
| Total Patients | Whole number |
| Average Visits per Patient | Decimal number, 2 places |
| Average Patient Age | Decimal number, 1 place |
| Average Patient Rating | Decimal number, 1 place |
| Average Wait Time | Decimal number, 1 place |
| Weekend Visit % | Percentage, 1 place |
| No-Show & Cancellation Rate | Percentage, 1 place |
| Gross Billed Revenue | Currency (CAD) |
| Public Coverage Amount | Currency (CAD) |
| Private Insurance Amount | Currency (CAD) |
| Total Insurance Coverage | Currency (CAD) |
| Insurance Coverage % | Percentage, 1 place |

## Report Usage

- **Executive Summary:** Total Visits, Rush Hour, Average Patient Rating, Average Wait Time, Insurance Coverage %, Weekday Visits, Weekend Visits, and Gross Billed Revenue.
- **Department Details:** Total Visits, Average Patient Rating, Average Wait Time, Gross Billed Revenue, No-Show & Cancellation Rate, and weekday/weekend workload.
- **Patient Analysis:** Total Patients, Average Patient Age, Average Visits per Patient, and Most Common Visit Type.

---

Developed with **Microsoft Power BI**, **Power Query**, and **DAX**.
