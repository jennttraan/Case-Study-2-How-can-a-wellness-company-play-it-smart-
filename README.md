 # How Can a Wellness Technology Company Play It Smart?

## A Google Data Analytics Professional Certificate Capstone Project

![Untitled design](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/bb956383-3407-4717-a809-d1412e0b8cf7)



Bellabeat is a company that develops fitness products for women. Their products include smart water bottles, fashionable fitness watches and jewelry, and yoga mats. Users can access their health data collected through these devices in the Bellabeat app.

Bellabeat’s co-founders would like to analyze data from non-Bellabeat fitness devices to see how consumers are using these products. The company hopes to use these insights to help guide new marketing strategies for the company.

## Ask

### Key stakeholders

* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer

* Sando Mu: Mathematician and Bellabeat’s cofounder

* The Bellabeat marketing analytics team: a team of data analysts responsible for collecting, analyzing, and reporting data that helps guide Bellabeat’s marketing strategy.

### Bellabeat products

* Bellabeat app: The Bellabeat app provides users with health data related to their activity, sleep, stress, menstrual cycle, and mindfulness habits. This data can help users better understand their current habits and make healthy decisions. The Bellabeat app connects to their line of smart wellness products.

* Leaf: Bellabeat’s classic wellness tracker can be worn as a bracelet, necklace, or clip. The Leaf tracker connects to the Bellabeat app to track activity, sleep, and stress.

* Time: This wellness watch combines the timeless look of a classic timepiece with smart technology to track user activity, sleep, and stress. The Time watch connects to the Bellabeat app to provide you with insights into your daily wellness.

* Spring: This is a water bottle that tracks daily water intake using smart technology to ensure that you are appropriately hydrated throughout the day. The Spring bottle connects to the Bellabeat app to track your hydration levels.

* Bellabeat membership: Bellabeat also offers a subscription-based membership program for users. Membership gives users 24/7 access to fully personalized guidance on nutrition, activity, sleep, health and beauty, and mindfulness based on their lifestyle and goals

### Business task

Analyze non-Bellabeat smart device data and compare with one Bellabeat product to discover insights to help guide marketing strategies for the company.

### Key Questions

1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?

## Prepare

### Data source:

FitBit Fitness Tracker Data on Kaggle in 18 CSV files. The data contains smart health data from personal fitness trackers for thirty fitbit users. The data was collected via a survey of personal tracker data, including minute-level output for physical activity, hear rate, and sleep monitoring, through Amazon Mechanical Turk between March 12, 2016 and May 12, 2016. It was updated two years ago as of August 2022. The data includes information about daily activity, steps, and heart rate.

### Limitations:

The sample size is small as only 30 individuals were considered.

The data is about six years old; the FitBit devices have likely evolved to deliver more accurate results.

Since the data was collected through a survey, the results may not be accurate as such participants may not provide honest and accurate answers.

Data pertaining to weight only has information from eight users. Furthermore, most entries in one of the fields are blank and about two-thirds of the weight entries were manually entered.

### Supporting data (future exploration):

As the FitBit data has the limitations listed above, an additional data source would be useful in the analysis. The Mi Band fitness tracker data (04.2016 - present) contains data relating to steps and sleep monitoring for one invididual from April 2016 to July 2022 collected from the Mi Band from Xiaomi. The data is stored in two CSV files, one for steps and one for sleep. Using this data, the analysis can also consider one individual over an extended period of time. The uploader of the data does note that there were about two weeks worth of step data that was corrupted so these data points were defaulted to zero.

## Process

### Choosing Data Files

As dailyActivity_merged.csv  provides a good summary of steps and calories burned and the sleepDay_merged.csv file provides sleep data these are good overall files to use to analyze patricipant usage. As fitness devices are generally used to track overall health and weight, the file weightLogInfo_merged containing weight data will also be used.

### Applications

Excel will be used to load and take an initial pass for issues, R to transform and explore the data, and Tableau to interactively visualize the data.

### First...

1. Make sure there are no blank entries in the data by using filters.
2. Convert Id field to text data type as no numerical equations are needed for this field.
3. Convert ActivityDate from Datetime to Date types as no times are given in the data.
4. In the `dailyActivity_merged.csv` file, there are many instances where TotalSteps is zero and SedentaryMinutes is 1440; the number of calories burned vary between users. This is most likely due to the weight and height of the user. There are a few instances where the sedentary minutes is 1440 but the calories burned is 0.
5. In the weightLogInfo_merged file, there are only two entries for the Fat field so this will not be used to draw insights.`

### Transform and Explore

All R code can be found here.

1. Load the tidyverse package and data files
2. Check to see if the data has been loaded correctly
3. Convert the Id field to character data type
4. Rename ActivityDate, SleepDay, and Date to convert to date data type<br>

 ```
activity <-activity %>%
  mutate_at(vars(Id), as.character) %>%
  mutate_at(vars(ActivityDate), as.Date, format = "%m/%d/%y") %>%
  rename("Day"="ActivityDate")
```
<br>

6. Combine data frames using left and right joins
7. Add day of the week variable <br>
```
combined_data <-sleep %>%
  right_join(activity, by=c("Id","Day")) %>%
  left_join(weight, by=c("Id", "Day")) %>%
  mutate(Weekday = weekdays(as.Date(Day, "m/%d/%Y")))
```
<br>
8. Filter and remove duplicate rows; count NAs and distinct entries using Id <br>
```combined_data <-combined_data[!duplicated(combined_data), ]
sum(is.na(combined_data))
n_distinct(combined_data$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
```
 <br>
 
The final data frame has 940 variables with 25 variables. There are 33 distinct Id entries total. The number of distinct users in dailyActivity, sleepDay, and weightLogInfo are 33, 24, and 8, respectively. There are 6893 NAs in the combined data. This is not surprising as there is only weight data from eight users and not all users logged sleep information.

## Analyze

## Select summary statistics and visualizations

```
combined_data %>%
select(TotalMinutesAsleep, TotalSteps, TotalDistance, VeryActiveMinutes, FairlyActiveMinutes, LightlyActiveMinutes, SedentaryMinutes, Calories, WeightKg, Fat, BMI, IsManualReport) %>%
summary()
```

![Summary Statistics](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/3fa555cb-1404-4396-9452-ee5a9662cb8e)


The average user weighs 72.04 kg, has a BMI of 25.19, and spent the most time doing light activities. On average, they also slept 6.9 hours, took 7638 steps, and traveled 5.49 km per day.

![Total steps by day](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/c4a06f8c-a08e-4740-870d-5bbafb9d3bc3)


Users took the most steps on Sundays and the least number of steps on Fridays. As all the values are fairly high, the marketing team can conclude that users value the step feature of health fitness devices. They could also assume that the feature will be very useful for Bellabeat customers.

![Minutes of moderate activity per day](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/d80401b9-18c0-4fc4-93d9-eb2b92f3c1d5)


It is interesting to see that the amount of time spent being fairly active decreased on Wednesdays and then picks back up on Thursdays. This may be due to the fact that most people are going back to work on Monday and then may get discouraged or tired by Wednesday. Wednesday may also be a popular rest day, allowing them to resume their activities on Thursday.

![Distribution of sleep time](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/a9868a46-add1-46f1-9498-d4656b536f72)


From the above histogram, most people slept between 312 and 563 minutes (between 5.2 and 9.4 hours). Note that this does not include the total time spent in bed resting.

![Total minutes Asleep vs Calories](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/93ff9f10-a16d-4143-84ed-4af4e50da150)


Besides a few outliers, calories were burned by those who slept between 5 and 7 hours. If only considering weight loss and calories burned, this aligns with the 5.2 to 9.4 hour sleep range, which may indicate that those who stay withint this range burn more calories.

![Logged Activities Distance](https://github.com/jennttraan/Case-Study-2-How-can-a-wellness-company-play-it-smart-/assets/144400508/1d5df55f-03f0-4e4b-8df7-f30758dc4d01)


The logged feature was not used too often as there were many blanks in the data and no records were available for Thursday and Friday. The highest days of logged distance were on the weekend or times when many people likely have free time to do physical activities.

## Share

Check out the dashboard on Tableau Public: [Bellabeat Dashboard](https://public.tableau.com/app/profile/jenny.tran8171/viz/Bellabeat_17157447917860/Dashboard#1)

## Act

The number of steps users took was the least on Friday which may be due to user becoming tired at the end of the week. As this is not limited to only FitBit customers, the marketing team could send notifications to users Thursday evenings and Friday and Saturday mornings encouraging users to continue being physical active throughout the day.

Many users did not use the Logged Distance feature on the FitBit devices. This suggests that users would prefer to have their data collected automatically. The Bellabeat marketing team can decide not to a feature activity distance log function as many users seem to not use this.

Compared to the data set size, there were very few entries for weight. Of those that were entered, about 2/3 were done manually. The individuals who did not log their weight may not have been concerned with losing weight or did not have the device needed to automatically record this data. Since many did not use the logged distance feature as well, the Bellabeat team could market weight devices like smart scales that automatically record this information.
