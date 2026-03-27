/*******************************************************************************
 * 01. CONSTANTS & CONFIGURATION
 *******************************************************************************/

// Static reference date for the dashboard
measure Today = DATE(2022, 6, 28)

// Defines the rolling window (in days) for KPI calculations
measure 'Calculation Period' = 30 


/*******************************************************************************
 * 02. CORE METRICS (Simple Aggregations)
 *******************************************************************************/

measure 'Avg Heart Rate' = AVERAGE(fctFitnessStats[Heart Rate])

measure 'Avg Daily Steps' = AVERAGE(fctFitnessStats[Steps])

measure 'Total Calories' = SUM(fctFitnessStats[Calories])

measure 'Avg Calories' = AVERAGE(fctFitnessStats[Calories])

measure 'Total Exercise Sessions' = SUM(fctFitnessStats[Exercise Session])


/*******************************************************************************
 * 03. USER TARGETS (Dynamic values from User Dimension)
 *******************************************************************************/

measure 'Avg Heart Rate - Target Min' = SELECTEDVALUE(dimUser[Heart Rate Target Min])

measure 'Avg Heart Rate - Target Max' = SELECTEDVALUE(dimUser[Heart Rate Target Max])

measure 'Avg Daily Steps - Target Min' = SELECTEDVALUE(dimUser[Steps Target Min])

measure 'Avg Daily Steps - Target Max' = SELECTEDVALUE(dimUser[Steps Target Max])

measure 'Avg Daily Calories - Target Min' = SELECTEDVALUE(dimUser[Calories Target Min])

measure 'Avg Daily Calories - Target Max' = SELECTEDVALUE(dimUser[Calories Target Max])

measure 'Total Exercise Sessions - Target Min' = SELECTEDVALUE(dimUser[Exercise Sessions (monthly) Target Min])

measure 'Total Exercise Sessions - Target Max' = SELECTEDVALUE(dimUser[Exercise Sessions (monthly) Target Max])


/*******************************************************************************
 * 04. CHART & AXIS FORMATTING (Visual Support Measures)
 *******************************************************************************/

measure 'Avg Heart Range' = [Avg Heart Rate - Target Max] - [Avg Heart Rate - Target Min]

measure 'Avg Heart Rate - Axis Fill' = 150 - [Avg Heart Rate - Target Max]

measure 'Avg Daily Steps Range' = [Avg Daily Steps - Target Max] - [Avg Daily Steps - Target Min]

measure 'Avg Daily Steps - Axis Fill' = 2000

measure 'Avg Calories Target Range' = [Avg Daily Calories - Target Max] - [Avg Daily Calories - Target Min]

measure 'Avg Calories - Axis Fill' = 1000

measure 'Total Exercise Sessions Range' = [Total Exercise Sessions - Target Max] - [Total Exercise Sessions - Target Min]

measure 'Total Exercise Sessions - Axis Fill' = 10


/*******************************************************************************
 * 05. ROLLING PERIOD LOGIC (Time Intelligence)
 *******************************************************************************/

measure 'Avg Heart Rate over Selected Period' = 
    VAR MaxDate = IF(ISFILTERED(dimDate), MAX(dimDate[Date]), [Today])
    VAR Result = 
        CALCULATE(
            AVERAGE(fctFitnessStats[Heart Rate]),
            REMOVEFILTERS(dimDate),
            DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] - 1, MaxDate)
        )
    RETURN Result

measure 'Avg Daily Steps over Selected Period' = 
    VAR MaxDate = IF(ISFILTERED(dimDate), MAX(dimDate[Date]), [Today])
    VAR Result = 
        CALCULATE(
            AVERAGE(fctFitnessStats[Steps]),
            DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] - 1, MaxDate)
        )
    RETURN Result

measure 'Avg Daily Calories over Selected Period' = 
    VAR MaxDate = IF(ISFILTERED(dimDate), MAX(dimDate[Date]), [Today])
    VAR Result = 
        CALCULATE(
            AVERAGE(fctFitnessStats[Calories]),
            REMOVEFILTERS(dimDate),
            DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] - 1, MaxDate)
        )
    RETURN Result

measure 'Total Exercise Sessions over Selected Period' = 
    VAR MaxDate = IF(ISFILTERED(dimDate), MAX(dimDate[Date]), [Today])
    VAR Result = 
        CALCULATE(
            SUM(fctFitnessStats[Exercise Session]),
            DATESBETWEEN(dimDate[Date], MaxDate - [Calculation Period] - 1, MaxDate)
        )
    RETURN Result


/*******************************************************************************
 * 06. HEALTH SCORE ALGORITHM (Proprietary Scoring)
 *******************************************************************************/

measure 'Health Score' = 
    // Logic: Absolute deviation from target midpoint normalized as %
    VAR CaloriesTarget = ([Avg Daily Calories - Target Max] + [Avg Daily Calories - Target Min]) / 2
    VAR CaloriesActual = [Avg Daily Calories over Selected Period]
    VAR CaloriesDelta = ABS(CaloriesActual - CaloriesTarget)
    VAR CaloriesDeltaPct = DIVIDE(CaloriesDelta, CaloriesTarget)
     
    VAR HeartRateTarget = ([Avg Heart Rate - Target Max] + [Avg Heart Rate - Target Min]) / 2
    VAR HeartRateActual = [Avg Heart Rate over Selected Period]
    VAR HeartRateDelta = ABS(HeartRateActual - HeartRateTarget)
    VAR HeartRateDeltaPct = DIVIDE(HeartRateDelta, HeartRateTarget)
     
    VAR StepsTarget = ([Avg Daily Steps - Target Max] + [Avg Daily Steps - Target Min]) / 2
    VAR StepsActual = [Avg Daily Steps over Selected Period]
    VAR StepsDelta = ABS(StepsActual - StepsTarget)
    VAR StepsDeltaPct = DIVIDE(StepsDelta, StepsTarget)
     
    VAR ExerciseSessionsTarget = ([Total Exercise Sessions - Target Max] + [Total Exercise Sessions - Target Min]) / 2
    VAR ExerciseSessionsActual = [Total Exercise Sessions over Selected Period]
    VAR ExerciseSessionsDelta = ABS(ExerciseSessionsActual - ExerciseSessionsTarget)
    VAR ExerciseSessionsDeltaPct = DIVIDE(ExerciseSessionsDelta, ExerciseSessionsTarget)
     
    VAR AvgDeltaPct = (CaloriesDeltaPct + HeartRateDeltaPct + StepsDeltaPct + ExerciseSessionsDeltaPct) / 4
    VAR ConditionShow = MAX(dimDate[Date]) <= [Today] || NOT ISFILTERED(dimDate)
    VAR Result = IF(ConditionShow, 1 - (AvgDeltaPct / 2))
    RETURN Result

measure 'Health Score - Fill to 100' = 1 - [Health Score]


/*******************************************************************************
 * 07. UI & PERSONALIZATION (User Experience Enhancement)
 *******************************************************************************/

// Dynamic Greeting logic
measure 'Welcome Text' = 
    VAR Hour = HOUR(NOW())
    VAR Greeting = 
        SWITCH(
            TRUE(),
            Hour >= 0 && Hour < 5, "Good Night",
            Hour >= 5 && Hour < 12, "Good Morning",
            Hour >= 12 && Hour < 18, "Good Afternoon",
            Hour >= 18 && Hour < 24, "Good Evening"
        )
    RETURN Greeting & " " & SELECTEDVALUE(dimUser[First Name])

// Base64 Image reconstruction from Image Table
measure DynamicPhoto = 
    "data:image/png;base64, " & 
    CONCATENATEX(
        'Iamge', 
        'Iamge'[fnSplitImage.ImagePart], , 
        'Iamge'[fnSplitImage.Index], ASC
    )
