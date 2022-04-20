# Google-Capstone-Bellabeat


### Purpose 
     
To practice data analytics from Google's Data Analytics course. I will be using this [guide](https://d18ky98rnyall9.cloudfront.net/QWpX1RwgS1CqV9UcILtQQA_76f613fdf2844940b0b31f8f0bb1b83f_Case-Study-2-_-How-can-a-wellness-technology-company-play-it-smart.pdf?Expires=1649980800&Signature=OdA48v0GOCZj8SK0T3rRR3sY8L3TyBe7U9q60rUKTmGl4lizFQuRUtfm-2gmshsKvmS8SUKHaEGnxU4l6~gkyJJ01o75zWfpK42o1Wwob1A9oG6mLVOPlkoZjApzRPE4vrz~rdDDTgI34978LP1tO9uhIS-80TPQH68v852gp7U_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A) as a reference for my first data analysis. In this post, I will explain my data analysis process guided by the strategies and procedures I have learned in this 8-part course.

This course is organized into 6 steps called the "Data Cycle"

1. Ask
2. Prepare
3. Process
4. Analyze
5. Share
6. Act

### Ask

In this step, we must have a clear idea of what business problem(s) we are tackling. Bellabeat, which is a "high-tech manufacturer of health-focused products for women," wants to be a more prominent company in the smart device market. Our stakeholders are the CEO, CFO, and the marketing team. These are the business questions that I would like to answer in this analysis:

1. What are some trends in smart device usage?
2. How could trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing?

**Business Task**: Analyze the Fitbit tracker data to discover trends customers portray when using their products and services for marketing strategies.

### Prepare

In this section of the Data Cycle, we want to understand where our data is coming from and what data we can use to help us with our business tasks. The data I will be using is the "[Fitbit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit/discussion?resource=download)" stored publicly on Kaggle. The data is organized into 18 different CSV files, and they are in long format. I will be using the data that contains the daily entries. These include Daily Activity, Daily Calories, Daily Intensities, Daily Steps, Sleep Day, and Weight Log Information. 

Now we will determine if the data"**ROCCCs**":

1. **Reliable**: Since the survey only has 30 users, it has low reliability.
2. **Original**: This data is not original because the survey was conducted by a 3rd party company called Amazon Mechanical Turk.
3. **Comprehensive**: I think that the 18 CSV file saves give a comprehensive insight into what the 30 customers use their Fitbit tracker for.
4. **Current**: This survey was completed between March 2016 to May 2016. It is 6 years old and not current.
5. **Cited**: There is more information about the dataset cited [here](https://zenodo.org/record/53894#.YkdD7i-B01K ).

Since this is a public dataset, we do not need to worry about addressing licensing, privacy, security, or accessibility.

Potential Problems with the data set include:
1. The sex of each participant is not disclosed. This is a problem because we are working with a company that focuses on women's health, and it would be better to align with the company's goal. 
2. The data does not include each participant's activity every day. This would make it a little more difficult to identify trends including their weight changes and their daily exercises.
3. Even though the data's description on Kaggle stated that there were 30 participants, I found 33 unique participants when I was cleaning the data. I decided to keep the 3 extra participants.

### Process

In the process phase, I determine what tools to use for the business task, how we can maintain the data's integrity, and clean the data. For the data cleaning process, I will be using google sheets since the data contain less than 1000 entries. I am most comfortable with google sheets, so I think it is a good tool to start off with. 

To ensure my data's integrity, I have made a copy of the data to be cleaned. I do this just in case I need to go back to the original data without having to download the data again. I also took a look at the data using conditional formatting and kept the data consistent between files and within variables. 

To ensure that the data is clean, I used google sheets tools to remove white space and to remove duplicates. I also identified null values with conditional formatting. I can verify that the data is cleaned and ready to be analyzed because we got rid of duplicates and white space, identified null values, and made values and variables consistent across each CSV file. I have documented the cleaning process directly onto the sheets.

On the Sleep Day sheet, I found and removed 3 duplicates, and I fixed the date formats on the Sleep Day column. On the Weight Log Information, I fixed the format of the date to match the rest of the format.

### Analyze

Now that I have cleaned the data in Google Sheets, it's time to transfer it over to R. I primarily wanted to use R because I wanted to implement the techniques Google's course has taught me. Also, I have a background in R from doing statistics in college.

Libraries used:
```{r}
library(tidyverse) ## Installs the tidyverse packages
library(readr) ##  To read CSV files
library(skimr) ## Summarizes data 
library(ggplot2) ## To create data visualizations
library(dplyr) ## For data manipulation
```

First, I uploaded the CSV files into RStudio and created variables to make them easier to access when coding.
```{r}
dailyActivity <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - dailyActivity_cleaned.csv")
dailyCalories <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - dailyCalories_cleaned.csv")
dailyIntensities <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - dailyIntensities_cleaned.csv")
dailySteps <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - dailySteps_cleaned.csv")
sleepDay <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - sleepDay_cleaned.csv")
weightLogInfo <- read_csv("~/Desktop/bellabeat_cleaned/Bellabeat Case Study - weightLogInfo_cleaned.csv")
```

Next, I coded a summary of the data of each sheet at a glance using `skim_without_charts`, `head`, and `colnames`.
```
skim_without_charts(dailyActivity)       ## Skim a data frame, getting useful summary statistics 
skim_without_charts(dailyCalories)
skim_without_charts(dailyIntensities)
skim_without_charts(dailySteps)
skim_without_charts(sleepDay)
skim_without_charts(weightLogInfo)

head(dailyActivity)                     ## Displays the first 5 rows of each column
head(dailyCalories)
head(dailyIntensities)
head(dailySteps)
head(sleepDay)
head(weightLogInfo)

colnames(dailyActivity)                  ## Displays the column names
colnames(dailyCalories)
colnames(dailyIntensities)
colnames(dailySteps)
colnames(sleepDay)
colnames(weightLogInfo)
```

I used `n_distinct` to see how many unique participants there are by using the Id as the indicator. I found 33 unique participants which did not align with the description of 30 participants given on Kaggle. Then I added `distinct()` to my variables so that I know there are no duplicate entries in my data.
```
n_distinct(dailyActivity$Id)
n_distinct(dailyCalories$Id)
n_distinct(dailyIntensities$Id)
n_distinct(dailySteps$Id)
n_distinct(sleepDay$Id)
n_distinct(weightLogInfo$Id)
```
```{r}
dailyActivity <- distinct(dailyActivity)
dailyCalories <- distinct(dailyCalories)
dailyIntensities <- distinct(dailyIntensities)
dailySteps <- distinct(dailySteps)
sleepDay <- distinct(sleepDay)
weightLogInfo <- distinct(weightLogInfo)
```
Then I revisit to see which file is good 
```{r}
n_distinct(dailyActivity$Id)
n_distinct(dailyCalories$Id) ## Calories are already in Activity
n_distinct(dailyIntensities$Id)
n_distinct(dailySteps$Id)
n_distinct(sleepDay$Id)      ## Not a great source because unique Id is less than 33
n_distinct(weightLogInfo$Id) ## Not a great source because unique Id is less than 33
```

I ran a statistical summary for dailyActivity using `summary()` and practiced using R's pipe syntax. An important measure to note was the average amount of steps taken per participant was 7638. A Medical News Today [article](https://www.medicalnewstoday.com/articles/how-many-steps-should-you-take-a-day) suggests that we should take 10,000 steps per day which equates to 5 miles (8 kilometers). Another note is that the minimum sedimentary minutes was 0. This seems to be a measuring error from the Fitbit tracker since I find it unlikely for someone to be on the move all the time in a span of a day.
```{r}
dailyActivity %>% 
  select(DailySteps,
         TotalDistance,
         SedentaryMinutes) %>% 
  summary()
```
Next, I did the same thing for the sleepDay data. Here, we see the participants spent, on average, 419.2 minutes asleep whereas 458.5 minutes were spent in bed. The difference between the average amount spent in bed and the amount asleep is 39.3 minutes. Healthline [suggests](https://www.healthline.com/health/healthy-sleep/how-long-does-it-take-to-fall-asleep#:~:text=The%20bottom%20line,fall%20asleep%20much%20more%20quickly.) we should be spending around 10-20 minutes before we fall asleep. Some good news is that our participants were getting 7 hours asleep on average, which is in the recommended range of 7-8 hours of sleep for adults.
```{r}
sleepDay %>% 
  select(TotalSleepRecords,
         TotalMinutesAsleep,
         TotalTimeInBed) %>% 
  summary()
```
An understandable pattern to think of is the amount of time a participant is in bed versus the amount of time they are asleep. I would expect to have a close relationship with each other since you are usually in bed when you are sleeping. And the graph illustrates this very close relationship: 
```{r}
ggplot(sleepDay, aes(TotalTimeInBed, TotalMinutesAsleep))+
  geom_point()+
  geom_smooth(method="lm", formula=y ~ x)+
  labs(title = "Minutes Spent Asleep and In Bed", subtitle = "Positive Correlation (n=33)")  
cor(sleepDay$TotalMinutesAsleep,sleepDay$TotalTimeInBed)
```
I also wanted to see the relationship between the number of intensities per day compared to the number of intensities recorded. I created a new column `total_activity` in the `dailyActivity` spreadsheet which adds the minutes of all 4 intensity levels. Then I created new columns for the percentage of each level in relation to the total activity.
```{r}
dailyActivity$total_activity <- dailyActivity$LightlyActiveMinutes + dailyActivity$FairlyActiveMinutes +
  dailyActivity$VeryActiveMinutes + dailyActivity$SedentaryMinutes
```
```{r}
dailyActivity$Percent_Sedentary <-(dailyActivity$SedentaryMinutes / dailyActivity$total_activity * 100)
dailyActivity$Percent_Light <- (dailyActivity$LightlyActiveMinutes / dailyActivity$total_activity * 100)
dailyActivity$Percent_Fair <- (dailyActivity$FairlyActiveMinutes / dailyActivity$total_activity * 100)
dailyActivity$Percent_Very <- (dailyActivity$VeryActiveMinutes / dailyActivity$total_activity * 100)
```
Below is the percentage of each level. 80% of the time with their Fitbit on is sedentary. 
```{r}
dailyActivity %>% 
  summarise_if(is.numeric, mean) %>% 
  select(Percent_Sedentary, 
         Percent_Light, 
         Percent_Fair, 
         Percent_Very)

df <- data.frame (intensity=c("Sedentary", "Light", "Fair", "Very"), 
                  percentage=c(80.0, 17.0, 1.20, 1.80))
ggplot(data=df, aes(x=intensity, y=percentage)) + 
  geom_bar(stat="identity")
```

Now, I wanted to see how active the participants are throughout the days of the week. I created a lighter version of the `dailyActivity` data frame into a smaller one called `activity_lite`. Then I formatted a new column name called `DayOfWeek`.
```{r}
activity_lite <- dailyActivity %>% 
  select(Id,ActivityDate,SedentaryMinutes,LightlyActiveMinutes,FairlyActiveMinutes,VeryActiveMinutes)

activity_lite$ActivityDate=as.POSIXct(dailyActivity$ActivityDate, format="%m/%d/%Y", tz=Sys.timezone())
activity_lite <- activity_lite %>% 
  rowwise() %>%
  mutate(DayOfWeek = weekdays(ActivityDate))
```
I created a total minus sedentary to see the amount of time each participant was active. I added the column into the `activity_lite` data frame, so then I measured the amount of time someone was active and for how long. This is then separated into facets by the days of the week. The chart shows that the participants are more active on Fridays rather than Tuesdays and Thursdays. However, the count of the Sundays activity has lower counts.
```{r}
activity_lite$total <- activity_lite$SedentaryMinutes + activity_lite$LightlyActiveMinutes +
  activity_lite$FairlyActiveMinutes + activity_lite$VeryActiveMinutes
activity_lite$total_minus_sedentary <- activity_lite$total - activity_lite$SedentaryMinutes

ggplot(data=activity_lite, aes(x=activity_lite$total_minus_sedentary)) +
  geom_histogram() +
  facet_wrap(~DayOfWeek)+
  labs(title = "Active Throughout the Weekdays", x="Active Minutes", y="Count of Occurance")
```

And there were 83 occurrences where the Fitbit did not measure any activity in a day.
```{r}
activity_lite %>% 
  count(total_minus_sedentary == 0) #There are 83 instances where participate did not exercise
```

### Share

In the Share stage, I am going to share the insights that I have gathered from analyzing the data. I believe that I will be able to answer the business questions presented in the Ask stage. The story my data told me is: 

- The more steps you take in a day, the more calories you are likely to burn.
- Some participants stay awake for periods of time before going to sleep.
- There are many days when our participants did not exercise.
- Tuesdays and Thursdays were the days when participants did not exercise. 

We can communicate this information to Bellabeat's marketing team, CEO, and CFO by sharing some areas we can help their customers improve and focusing on how their devices will help bridge the gap to reach their customer's fitness and health goals. I can use the data illustrations I have created from R to help illustrate these ideas. In the Share phase, I would also need to make sure that these findings are accessible to the people I have presented to. I would also like to remind my stakeholders the shortcomings of the data have been that we do not know the gender of the participants and it may not be the most accurate data to use for the business.

### Act

The final conclusion in my analysis is to concentrate our marketing team to point out the shortcomings in our participants' data to strengthen our product's desirability to women who want to lead a more healthy and active lifestyle. We can expand on the importance of exercising even when it's difficult. On days like Tuesdays and Thursdays, when it's difficult to get started on exercising, our products can remind our users to get started. We can also implement a step goal for people who want to burn calories to lose weight. We can also remind our users when it's time to put the book or phone down when they have spent too much time in bed not falling asleep. Bellabeat can help break bad habits that might arise from our users, and help them lead a healthy life. 
