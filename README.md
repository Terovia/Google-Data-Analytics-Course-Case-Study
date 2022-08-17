# Google Data Analytics Course Case Study (Bellabeat)

<p align="center">
  <img 
    width="300"
    height="300"
    src="https://media.giphy.com/media/JWuBH9rCO2uZuHBFpm/giphy.gif"
  >
</p>

## Introduction
In the last course of the Google Data Analytics Professional Certificate course, students are allowed to complete a case study. This case study aims to provide students with the ability to utilize the skills learned in the class and, hopefully, can kick start a Data Analysis career through this case study.

This case study applies the data analysis process: **ask, prepare, process, analyze, share,** and **act**. From herein, I will use this process and effectively explain my analysis to achieve the business task.

### Scenario
I am a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a more significant player in the global smart device market. Urška Sršen, co-founder and Chief Creative Officer of Bellabeat, believes that analyzing smart device fitness data could help unlock new growth opportunities for the company. I've been asked to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers use their smart devices. The insights I discover will then help guide the marketing strategy for the company. I will present my analysis to the Bellabeat executive team and my high-level recommendations for Bellabeat’s marketing strategy.

## Ask Phase
### Business Task

**Determine which Bellabeat product to market more based on the trend and usage of a fitness data tracking device so that Bellabeat can market effectively and efficiently.**

### Stakeholders
- **Urška Sršen**: Bellabeat’s co-founder and Chief Creative Officer
- **Sando Mur**: Mathematician and Bellabeat’s co-founder; a vital member of the Bellabeat executive team
- **Bellabeat marketing analytics team**: A team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy


## Prepare Phase
### Background of the Data
The dataset used is [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit "https://www.kaggle.com/datasets/arashnic/fitbit"), which is publicly available through Kaggle. This dataset was recorded from April 12, 2016, to May 12, 2016. It consists of thirty (30) Fitbit users who consented to submit their tracker data, including a minute-level output for physical activity, heart rate, and sleep monitoring.

### Data Credibility and Integrity
1. A survey generated the dataset via Amazon Mechanical Turk, a third-party source. As a result, we cannot determine the credibility of this data. 
2. Additionally, we are unsure of the participants' age, gender, and demographics, which is a limitation of this analysis. We know that Bellabeat specializes in health-related products for women, so this data may not be credible for the business task. Therefore, we can potentially encounter sampling bias in our analysis. 
3. The dataset is outdated since it was collected in 2016. Technology, especially smart watch technology, rapidly changes, so the data obtained may not be 100% accurate. For instance, the ability to track steps via smartwatch is not 100% accurate, often with -10% tolerance.

### Data Organization and Description
The dataset downloaded is observed to be in Microsoft Excel CSV file format. There are 18 CSV files, and a description of each one is shown in the table below.

Table Name | Description
| :--- | :---
dailyActivity_merged  | Daily activity of 33 users in 31 day period. Tracks total steps, total distance, logged activities (in distance), intensities in minutes and distance, and calories
dailyCalories_merged  | Daily calorie burn of 33 users in 31 day period
dailyIntensities_merged | Daily intensities of 33 users in 31 day period. Tracks intensities in minutes and distance
dailySteps_merged | Daily steps of 33 users in 31 day period
heartrate_seconds_merged | Heartrate monitor of 7 users every second in 31 day period
hourlyCalories_merged | Hourly calorie burn of 33 users in 31 day period
hourlyIntensities_merged | Hourly intensity and average intensity of 33 users in 31 day period
hourlySteps_merged | Hourly steps of 33 users in 31 day period
minuteCaloriesNarrow_merged	| Minute recording of calories burned by 33 users in 31 day period (single row format)
minuteCaloriesWide_merged |  Minute recording of calories burned by 33 users in 31 day period (single column format) 
minuteIntensitiesNarrow_merged | Minute recording of intensities of 33 users in 31 day period (single row format)
minuteIntensitiesWide_merged | Minute recording of intensities of 33 users in 31 day period (single column format)
minuteMETsNarrow_merged | Minute recording of Metabolic equivalent (MET) of 27 users in 31 day period (single row format)
minuteSleep_merged | Minute recording of sleep logs of 24 users over 31 days. The value column consists of integers 1, 2, and 3, unknown values.
minuteStepsNarrow_merged	| Minute recording of steps of 33 users in 31 day period (single row format)
minuteStepsWide_merged | Minute recording of steps of 33 users in 31 day period (single column format)
sleepDay_merged | Sleep log of 24 users in 31 day period. Tracks number of sleep in a day (nap or sleep), total minutes asleep, and total time in bed.
weightLogInfo_merged | Weight log (pounds and kilograms) of 8 users in 31 days. Body fat percentage and BMI output.
 
# Process Phase
I will be utilizing Microsoft Excel and Google BigQuery to ensure the dataset is clean, free from errors, and ready for analysis.

My analysis will focus on the following datasets:
- dailyActivity_merged (renamed to dailyActivity from herein)
- dailySteps_merged (renamed to dailySteps from herein)
- sleepDay_merged (renamed to sleepDay from herein)

## Cleaning Process
To determine the data integrity of the datasets, I wanted to see if there were any duplicates or outliers.
 
*dailyActivity*:
  - I used the Pivot table function in Excel to verify the period of the analysis and the sample size of the users. After verifying that the data collected was indeed 31 days with 33 unique IDs, I proceeded to identify any duplicates or outliers.
  - In Excel, I filtered and removed entries with data that shows zero (0) steps taken. The justification for this removal was that the user was probably not wearing the Fitbit that day; therefore, it is not credible for analysis, as we are seeking any activity that day.
  - I sorted the Calories column and observed 11 users burnt less than 1200 calories a day (based on a calculation that generally 75 calories are burnt an hour for doing nothing for 16 hours). I still kept this data intact because I will later explain that this data is still credible for the analysis to determine how often people wore their Fitbit. 
 
*dailySteps*:
 - In Excel, I filtered and removed zero (0) steps taken because we are looking for an activity and wear usage of Fitbit. 
 
*sleepDay*:
 - I observed that this data was already clean, and no action was taken.
 
Below are a few SQL queries to determine the count of users' start and end date of the data.
```
 -- First, set up the project by designating variables for regular expression based analyses
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
 
-- Verify dataActivity table shows a list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.dailyActivity;
-- Returns 33 unique IDs
 
-- Verify dailySteps table shows a full month of recording
SELECT MIN(ActivityDay) AS startDate, MAX(ActivityDay) AS endDate
FROM fitbit_data.dailySteps;
-- Returned startDate as 2016-04-12 and endDate as 2016-05-12
 
-- Verify dailySteps table shows a list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.dailySteps;
-- Returns 33 unique IDs
 
-- Verify sleepDay table shows a list of users/participants
SELECT DISTINCT Id
FROM fitbit_data.sleepDay;
-- Returns 24 unique IDs
```
 
We noticed that column Id is the primary key throughout these tables, which I used to my advantage to join tables if necessary. I also verified the dataset using the following SQL query:
 
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
I will dive deep into the clean dataset to answer the business task in analyzing and sharing phases. Specifically, I will look at the Fitbit's daily usage to check how many people are wearing their Fitbits. I also want to check their physical activity usage to see what types of people are using Fitbits, and lastly, review the relationship between daily steps to calories burned, specifically on what day of the week and the average amount of sleep in a week.

For this exercise, I used the following tools for analysis:
- Google's platform, **BigQuery** for SQL
- **Tableau Public** (see my profile [here](https://public.tableau.com/app/profile/anthony.montero#!/)) for generation of illustrations (pie charts and bar graphs), 
-  **R Studio Cloud** (R programming language) for the four scatter plots in this section
    - The packages I used were tidyverse and ggpubr

## Physical Activity
First, since we cannot determine what kind of Fitbit users are, to understand better what kind of participants submitted their data, I want to look at the relationship between daily steps taken to their physical activity. [This article discusses the different types of physical activity one can gain from the number of daily steps.](https://www.medicinenet.com/how_many_steps_a_day_is_considered_active/article.htm)


![Sheet 1 (1)](https://user-images.githubusercontent.com/90084874/169169023-88ffcbca-b61f-42e8-9535-481b2caec13c.png)

Based on the pie graph above, we identified a mixed group of active people - about 50% of the users are sedentary (21%) and low active (27%), while the other 50% are users who take more than 7,500 steps. **This gives us a better understanding of who Fitbit users are; they can range from all different activity levels.**



_The following SQL query aided the generation of the pie graph:_
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
```

## Fitbit Usage
Since we know, many different types of active people wear Fitbit, we now want to determine the wear usage to understand when users wear their smartwatches.

![Dashboard 1 (2)](https://user-images.githubusercontent.com/90084874/169906529-5fd30c2a-506d-4239-8472-5c642ad5ac86.png)

I took a look at a daily and monthly rate of wear. We can see that the Fitbit is worn positively, with cases where it's worn for more than half a day to an almost full day. We also see that more than 75% of the participants wear their watch for more than 21 days of the month. Perhaps we can consider there were days when people wore a traditional watch. **In any way, we can observe that people wear their Fitbits frequently - most of the day and worn frequently for more than 20 days throughout a month.**



_The following SQL queries were used:_
- For the horizontal bar graphs:
```
-- Let's see how often users wore their FitBit.
-- Categorized it by All Day Usage, More than Half Day Usage, and Less than Half Day Usage
SELECT
Id, FORMAT_TIMESTAMP("%A", ActivityDate) AS day_of_week,
CASE WHEN ((VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes + SedentaryMinutes)/1440)*100 = 100 THEN 'All Day Usage'
  WHEN ((VeryActiveMinutes + FairlyActiveMinutes + LightlyActiveMinutes + SedentaryMinutes)/1440)*100 BETWEEN 50 AND 100 THEN 'More than Half Day Usage'
  ELSE 'Less than Half Day Usage'
END AS usage,
FROM fitbit_data.dailyActivity
ORDER BY Id;
-- We see the frequency of when user’s wear their watch. We can count how many and plot a pie chart and see the percentage of each category.
```

- For the pie chart:
```
SELECT DISTINCT Id, COUNT(ActivityDate) AS num_of_activedays,
CASE 
  WHEN COUNT(ActivityDate) < 10 THEN 'Low Usage'
  WHEN COUNT(ActivityDate) BETWEEN 11 AND 20 THEN 'Moderate Usage'
  WHEN COUNT(ActivityDate) BETWEEN 20 AND 31 THEN 'High Usage'
  ELSE 'Error'
END AS typical_usage
FROM fitbit_data.dailyActivity
GROUP BY Id
ORDER BY num_of_activedays ASC;
```


## Steps
After analyzing our participants, we now want to know the intermediate steps in the 31 days, specifically at what day of the week. Understanding this will determine the most active day to the least busy day.

![Sheet 4 (1)](https://user-images.githubusercontent.com/90084874/169905162-cd96d349-473a-4af3-934a-c261fdcb6dd0.png)

The first thing we noticed was the dip in step activity on Sunday. While often classified as a rest day, we can understand why many people would rather stay indoors on Sunday in preparation for the week. On the other hand, we see a positive trend that many people walk more than 7,500 steps throughout the day. More importantly, we see a trend that people walk for more than 8,500 steps on Monday, Tuesday, and Saturday. This exercise is still good, but according to [CDC guidelines](https://www.cdc.gov/diabetes/prevention/pdf/postcurriculum_session8.pdf), most adults should aim for a daily goal of 10,000 steps, and in one month, we noticed that our participants collectively did not reach an average daily goal of 10,000 steps. **We can determine that most people can work on a daily plan of 10,000 steps.**

_The following SQL query was used:_
```
# Below is a list of distinct IDs with average steps for the whole month split into days of the week.
 SELECT
   AVG(TotalSteps) AS avg_steps,
   FORMAT_TIMESTAMP("%A", ActivityDate) AS day_of_week
 FROM fitbit_data.dailyActivity
 GROUP BY day_of_week
 ORDER BY avg_steps DESC;
```

## Relationship Between Activity to Calorie Burn
In this section, I used **R Studio Cloud's** R programming language to develop four (4) scatter plots to illustrate the different types of activities that burn the most calories.

![Different Activities to Calories Burn](https://user-images.githubusercontent.com/90084874/169434593-72ed6311-de4c-4795-ab5c-48c2683be485.png)

I plotted the different types of activity spent in minutes to calories burned. Using the regression line, we can see that being very active had the most substantial effect on daily calorie burn in plot D. **We can conclude that the more active you are, the more calories you burn.**

_The following code in R was used:_
```
sm <- ggplot(data=daily_activity, aes(x=SedentaryMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=0, label.y=3700) + stat_cor(aes(label=..r.label..), label.x=0, label.y=3200)

la <- ggplot(data=daily_activity, aes(x=LightlyActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=0, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=0, label.y=4200)

fa <- ggplot(data=daily_activity, aes(x=FairlyActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=90, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=90, label.y=4200)

va <- ggplot(data=daily_activity, aes(x=VeryActiveMinutes, y=Calories)) + geom_point() + geom_smooth(method = "lm") + stat_regline_equation(label.x=20, label.y=4700) + stat_cor(aes(label=..r.label..), label.x=20, label.y=4200)

ggarrange(sm, la, fa, va, labels = c("A", "B", "C", "D"), ncol = 2, nrow = 2)
```

## Average Sleep
Lastly, let's look at the sleep log. 

![Sheet 5 (1)](https://user-images.githubusercontent.com/90084874/169905330-9dfb4e3d-2fa1-4cb4-9483-7db830794769.png)

According to the [National Sleep Foundation guidelines](https://pubmed.ncbi.nlm.nih.gov/29073412/), it is recommended for adults (18 to 64 years old) to sleep at a minimum of _7 to 9 hours_. We reviewed our participants who logged their sleep times and discovered an interesting trend. During the middle of the week, Wednesday, and weekends (Saturday and Sunday), people were sleeping at a minimum of 7 hours. However, on other nights, people struggled to obtain 7 hours of sleep. **We can conclude that only 3 days of the week, people are sleeping the minimum 7 hours.**


_The following SQL query was used:_

_Note that this uses the INNER JOIN function to join sleepDay table to dailyActivity table._
```
SELECT 
  d.Id, d.ActivityDate, TotalMinutesAsleep, TotalMinutesAsleep/60 AS TotalHoursAsleep,
  FORMAT_TIMESTAMP("%A", ActivityDate) AS day_of_week
FROM fitbit_data.dailyActivity AS d 
INNER JOIN fitbit_data.sleepDay AS s 
ON d.Id = s.Id AND d.ActivityDate = s.SleepDay
ORDER BY ActivityDate ASC;
```


# Recommendations
We have discovered that different types of people wear smartwatches. They can range in many activity levels, from sedentary to very active people. We also noticed that people often wear their smartwatches because they offer many features, such as tracking steps, heart rate, calorie burn, and sleep.

Even though smartwatches offer many cool features that can better their health, we noticed that not many people walk a goal amount of 10,000 steps. As such, Bellabeat should notify users to reach 10,000 steps. **One way is to have a daily reminder of how many more remaining steps to get 10,000 steps.** **If Bellabeat can also obtain geographical information, relay that to the user and suggest a scenic route to complete their goal, this can be very useful for people who prefer to walk around scenic areas.**

We also looked at how being very active can lead to more calorie burn. **If Bellabeat can promote their devices used in high-intensity interval training, users will start seeing how effectively their devices record heart rate and calorie burn in an exercise session.**

Lastly, **Bellabeat should promote better sleep reminders and encourage users to wear their Bellabeat Leaf (watch) to sleep.** **Although wearing a watch can be uncomfortable while sleeping, Bellabeat should look into comfortable watch bands so that people will often wear their watch all day.** **We also see that their Bellabeat Leaf can last for 6 months, which doesn’t require overnight charging. If Bellabeat can advertise this functionality, people will more likely equip their Bellabeat Leaf all the time, even while sleeping. **
