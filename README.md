# Google Data Analytics Course Case Study (Bellabeat)

<p align="center">
  <img 
    width="300"
    height="300"
    src="https://media.giphy.com/media/JWuBH9rCO2uZuHBFpm/giphy.gif"
  >
</p>

## Introduction
In the last course of the Google Data Analytics Professional Certificate course, students are given the opportunity to complete a case study.  The aim of this case study is to provide students the ability to utilize the skills learned in the course and hopefully, through this case study, can kick start a Data Analysis career.

This case study focuses on applying the data analysis process: **ask, prepare, process, analyze, share,** and **act**. I am tasked to perform an analysis for a company called, Bellabeat.

### Scenario
I am a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing smart device fitness data could help unlock new growth opportunities for the company. I've been asked to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. The insights I discover will then help guide marketing strategy for the company. I will present my analysis to the Bellabeat executive team along with my high-level recommendations for Bellabeat’s marketing strategy.

## Ask Phase
### Business Task

**Determine which Bellabeat product to market more based on the trend and usage of a fitness data tracking device, so that Bellabeat can market effectively and efficiently.**

### Stakeholders
- **Urška Sršen**: Bellabeat’s cofounder and Chief Creative Officer
- **Sando Mur**: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team
- **Bellabeat marketing analytics team**: A team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy

## Prepare Phase
### Background of the Data
The dataset used is [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit "https://www.kaggle.com/datasets/arashnic/fitbit"), which is publicly available through Kaggle. The dataset was recorded from April 12, 2016 to May 12, 2016 and consists of thirty (30) Fitbit users who consented to submitting their personal tracker data, which includes minute-level output for physical activity, heart rate, and sleep monitoring.

### Data Credibility and Integrity
1. The dataset was generated by a survey via Amazon Mechanical Turk, in other words, a third party source. Therefore, we cannot determine the credibility of this data. 
2. We are unsure of the participants age, gender, and demographics, which is a limitation to this analysis. We know that Bellabeat specializes in health-related products for women, so this data may not be credible for the business task. Therefore, we can potentially encounter sampling bias in our analysis. 
3. The dataset is outdated since it was collected in 2016. Technology, especially smart watch technology, rapidly changes, so the data obtained may not be 100% accurate. For instance, the ability to track steps via smart watch is not 100% accurate, often with -10% tolerance.

### Data Organization and Description
The dataset downloaded is observed to be in Microsoft Excel CSV file format. There are 18 CSV files and a description of each one is shown in the table below.

Table Name | Description
| :--- | :---
dailyActivity_merged  | Daily activity of 33 users in 31 day period. Tracks total steps, total distance, logged activities (in distance), intensities in minutes and distance, and calories
dailyCalories_merged  | Daily calorie burn of 33 users in 31 day period
dailyIntensities_merged | Daily intensities of 33 users in 31 day period. Tracks intensities in minutes and distance
dailySteps_merged | Daily steps of 33 users in 31 day period
heartrate_seconds_merged | Heartrate monitor of 7 users in every second in 31 day period
hourlyCalories_merged | Hourly calorie burn of 33 users in 31 day period
hourlyIntensities_merged | Hourly intensity and average intensity of 33 users in 31 day period
hourlySteps_merged | Hourly steps of 33 users in 31 day period
minuteCaloriesNarrow_merged	| Minute recording of calories burn of 33 users in 31 day period (single row format)
minuteCaloriesWide_merged |  Minute recording of calories burn of 33 users in 31 day period (single column format) 
minuteIntensitiesNarrow_merged | Minute recording of intensities of 33 users in 31 day period (single row format)
minuteIntensitiesWide_merged | Minute recording of intensities of 33 users in 31 day period (single column format)
minuteMETsNarrow_merged | Minute recording of Metabolic equivalent (MET) of 27 users in 31 day period (single row format)
minuteSleep_merged | Minute recording of sleep logs of 24 users over 31 days. Value column consist of integers 1, 2, and 3, which are unknown values.
minuteStepsNarrow_merged	| Minute recording of steps of 33 users in 31 day period (single row format)
minuteStepsWide_merged | Minute recording of steps of 33 users in 31 day period (single column format)
sleepDay_merged | Sleep log of 24 users in 31 day period. Tracks number of sleep in a day (nap or sleep), total minutes asleep, and total time in bed.
weightLogInfo_merged | Weight log (pounds and kilograms) of 8 users in 31 day period. Body fat percentage and BMI output.
 
# Process Phase
I will be utilizing Microsoft Excel and Google BigQuery to ensure the dataset is clean, free from errors, and ready for analysis.

My analysis will focus on the following datasets:
- dailyActivity_merged (renamed to dailyActivity from herein)
- dailySteps_merged (renamed to dailySteps from herein)
- sleepDay_merged (renamed to sleepDay from herein)

## Cleaning Process
To determine the data integrity of the datasets, I wanted to see if there were any duplicates or outliers.
 
*dailyActivity*:
  - I used the Pivot table function in Excel to verify the time period of the analysis and the sample size of the users. After verifying that the data collected was indeed a 31 day period with 33 unique IDs, I proceeded to identify any duplicates or outliers.
  - In Excel, I filtered and removed entries with data that shows zero (0) steps taken. The justification for this removal was that the user was probably not wearing the Fitbit that day, therefore it is not credible for analysis, as we are seeking for any activity that day.
  - I sorted the Calories column and observed 11 users burnt less than 1200 calories a day. 1200 calories were assumed based on a calculation that 75 calories burnt an hour for absolutely doing nothing for 16 hours. I still kept this data intact because I will later explain that this data is still credible for the analysis to determine how often people wore their Fitbit. 
 
*dailySteps*:
 - In Excel, I filtered and removed zero (0) steps taken. As discussed above, we are looking for activity and wear usage of Fitbit.
 
*sleepDay*:
 - It was observed that this data was already clean and no action was taken.
 
The following SQL queries were also used to determine the count of users, start and end date of the data.
```
 -- First, setup the project with designating variables for regular expression based analyses
DECLARE
 TIMESTAMP_REGEX STRING DEFAULT r'^\d{4}-\d{1,2}-\d{1,2}[T ]\d{1,2}:\d{1,2}:\d{1,2}(\.\d{1,6})? *(([+-]\d{1,2}(:\d{1,2})?)|Z|UTC)?$';
DECLARE
 DATE_REGEX STRING DEFAULT r'^\d{4}-(?:[1-9]|0[1-9]|1[012])-(?:[1-9]|0[1-9]|[12][0-9]|3[01])$';
DECLARE
 TIME_REGEX STRING DEFAULT r'^\d{1,2}:\d{1,2}:\d{1,2}(\.\d{1,6})?$';
 
-- Verify dataActivity table shows a full month of recording
SELECT MIN(ActivityDate) AS startDate, MAX(ActivityDate) AS endDate
FROM fitbit_data.dailyActivity;
-- Returned startDate as 2016-04-12 and endDate as 2016-05-12
 
-- Verify dataActivity table shows list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.dailyActivity;
-- Returns 33 unique IDs
 
-- Verify dailySteps table shows a full month of recording
SELECT MIN(ActivityDay) AS startDate, MAX(ActivityDay) AS endDate
FROM fitbit_data.dailySteps;
-- Returned startDate as 2016-04-12 and endDate as 2016-05-12
 
-- Verify dailySteps table shows list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.dailySteps;
-- Returns 33 unique IDs
 
-- Verify sleepDay table shows list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.sleepDay;
-- Returns 24 unique IDs
```
 
In order to better understand our data, we noticed that column Id is the primary key that was used throughout these tables. I also verified it using the following SQL query:
 
```
-- Verify Id column is in every table. has_id_column shows 1 for TRUE or 0 for FALSE
SELECT table_name,
 SUM(CASE
   WHEN column_name = "Id" THEN 1
   ELSE 0
 END) AS has_id_column
FROM `fitbit_data.INFORMATION_SCHEMA.COLUMNS`
GROUP BY 1
ORDER BY 1 ASC;
 -- Returns value 1 for every table, therefore it is true that every table used has an Id column.
```

# Analyze and Share Phases
In these phases, I will dive deep into the analysis process using the clean datasets in order to answer the business task. I will look at the Fitbit daily usage in order to see if how many people are wearing their Fitbits, their physical activity cases to see what kind of people are using Fitbits, the relationship between daily steps to calories burn specifically on what day of week, and the correlation of physical activities to the average sleep. 

To better understand the participants, using this article, [https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm](https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm, "https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm"), I want to see the relationship of how many steps taken daily to their physical activity. 

Using Tableau, I created the following pie graph:
![Sheet 1 (1)](https://user-images.githubusercontent.com/90084874/169169023-88ffcbca-b61f-42e8-9535-481b2caec13c.png)

Based on this pie graph, we can see a mixed group of active people. About 50% of the users are sedentary (21%) and low active (27%), while the other 50% are users who take more than 7,500 steps. This gives us a better understanding of who Fitbit users are - they can range from all different activity levels.

The following SQL query aided the generation of the pie graph:
```
# For 33 IDs, check for the case usage of each individual per their activity level
# We look at the average daily steps and relate that to activity level
# Source: https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm
SELECT DISTINCT Id, AVG(StepTotal) AS avg_steps,
 CASE
  WHEN AVG(StepTotal) < 5000 THEN 'Sedentary'
  WHEN AVG(StepTotal) BETWEEN 5000 AND 7499 THEN 'Low Active'
  WHEN AVG(StepTotal) BETWEEN 7500 AND 9999 THEN 'Somewhat Active'
  WHEN AVG(StepTotal) BETWEEN 10000 AND 12500 THEN 'Active'
  ELSE 'Highly Active'
 END AS activity_level
FROM fitbit_data.dailySteps
GROUP BY Id
ORDER BY avg_steps ASC;
# Create table to determine activity levels in this case study
```

Since we know the Fitbit is worn by many different users, we want to determine the wear usage to further understand when users wear their smart watches.
In the following set, I distribute across the  

![Dashboard 1 (2)](https://user-images.githubusercontent.com/90084874/169906529-5fd30c2a-506d-4239-8472-5c642ad5ac86.png)


## Steps?

We now want to know the average steps in the 31 day period, specifically at what day of the week. This will help us determine what's the most active day to the least active day.

TBD: Explain relationship of image.
According to [CDC guidelines](https://www.cdc.gov/diabetes/prevention/pdf/postcurriculum_session8.pdf), most adults should aim for a daily goal of 10,000 steps.


![Sheet 4 (1)](https://user-images.githubusercontent.com/90084874/169905162-cd96d349-473a-4af3-934a-c261fdcb6dd0.png)

```
# Below is a list of distinct IDs with average steps for the whole month split into days of the week.
 SELECT
   AVG(TotalSteps) AS avg_steps,
   FORMAT_TIMESTAMP("%A", ActivityDate) AS day_of_week
 FROM fitbit_data.dailyActivity
 GROUP BY day_of_week
 ORDER BY avg_steps DESC;
# This case study shows that people collected were somewhat active majority of the month (7500 steps and above)
# We determined that Tuesday and Saturday had the most average steps at 9000 steps.
# Sunday was the least active day, but still reached the minimum of 7500 steps.
```

## Calories Burn vs. Steps
In this section, I used **R Studio Cloud's** R programming language to develop four (4) scatter plots to illustrate the different types of activities that burn the most calories.

I installed tidyverse and ggpubr.
```
install.packages('tidyverse')
library(tidyverse)
```
```
install.packages('ggpubr')
library(ggpubr)
```

```
sm <- ggplot(data=daily_activity, aes(x=SedentaryMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=0, label.y=3700) + stat_cor(aes(label=..r.label..), label.x=0, label.y=3200)

la <- ggplot(data=daily_activity, aes(x=LightlyActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=0, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=0, label.y=4200)

fa <- ggplot(data=daily_activity, aes(x=FairlyActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=90, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=90, label.y=4200)

va <- ggplot(data=daily_activity, aes(x=VeryActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=20, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=20, label.y=4200)

ggarrange(sm, la, fa, va, labels = c("A", "B", "C", "D"), ncol = 2, nrow = 2)
```
![Different Activities to Calories Burn](https://user-images.githubusercontent.com/90084874/169434593-72ed6311-de4c-4795-ab5c-48c2683be485.png)

## Average Sleep per Week


![Sheet 5 (1)](https://user-images.githubusercontent.com/90084874/169905330-9dfb4e3d-2fa1-4cb4-9483-7db830794769.png)

