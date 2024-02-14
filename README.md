# **Cyclistic Case Study (Porfolio_1)**
 A case study focused on a bicycle rental company.
## Scenario
As a junior data analyst within the marketing analysis team at Cyclistic, a Chicago-based bike-share company, the director of marketing is convinced that the company's future prosperity hinges on optimizing the count of annual memberships. Consequently, our team is keen on unraveling the disparities in how casual riders and annual members utilize Cyclistic bikes. Armed with these findings, we aim to formulate a fresh marketing strategy tailored to transition casual riders into becoming annual members. However, it is imperative that our recommendations garner approval from Cyclistic executives, demanding the support of compelling data insights and professionally crafted data visualizations.
## Stakeholders
> 1. **Lily Moreno:** The responsibility for developing campaigns and initiatives to promote the bike-share program lies with both the director of marketing. 
> 2. **Cyclistic marketing analytics team:** A team of data analysts is entrusted with the task of collecting, analyzing, and reporting data to inform Cyclistic's marketing strategy.
> 3. **Cyclistic executive team:** A team known for its meticulous attention to detail will determine whether to endorse the proposed marketing program.
## Preparing the data
The study must be carried out with historical travel data in the last 12 months using Cyclistic, to analyze and identify trends. The repository can be found [here](https://divvy-tripdata.s3.amazonaws.com/index.html).

I have used the data for the 12 months of 2023, as it is the most recent. At first, I decided not to delete any columns since there was no sensitive information (such as credit cards) and everything was anonymized.

The data is made up of 12 .csv each with 13 columns. In total, there are 5,712,861 rows of data.
##Tools
I have decided to use R for convenience. First of all, when managing such a large number of rows and columns at the same time, I ruled out using spreadsheets, as it could be time-consuming and I found it much less convenient.

My choice of R over Python comes from me feeling more comfortable in this ecosystem.

I have carried out the entire process in RStudio following guidelines and correcting errors.
## Transforming data
The first thing was to combine the 12 files into one so that I could work with all the data at the same time when necessary. I used the **bind_rows()** function to bind all the files together.

```
y2023_F = bind_rows(
  y2023_01,
  y2023_02,
  y2023_03,
  y2023_04,
  y2023_05,
  y2023_06,
  y2023_07,
  y2023_08,
  y2023_09,
  y2023_10,
  y2023_11,
  y2023_12
)
```

From that moment on, create a **head()** of the first 100 rows to be able to work faster and test code.

First, I checked the data type which were the **started_at** and **ended_at** columns, which were strings. Then I used the **asDate()** function to change them to dates.

From that change, I created four more columns where it was recorded: the day, the month, the year and the day of the week.

```
y2023_F$date <- as.Date(y2023_F$started_at)
y2023_F$month <- format(as.Date(y2023_F$date), "%m")
y2023_F$day <- format(as.Date(y2023_F$date), "%d")
y2023_F$year <- format(as.Date(y2023_F$date), "%Y")
y2023_F$day_of_week <- format(as.Date(y2023_F$date), "%A")
```

At the same time, I eliminated four columns that, in my opinion, were not going to be useful for the analysis: the latitudes and longitudes of the stations.

```
y2023_combined = y2023_combined %>%
  select(-c(start_lat, start_lng, end_lat, end_lng))
```

Next, I created a new column called **ride_length** to have the duration of each ride. I did this by applying the **difftime()** function inside **as.numeric** between the **ended_at** and **started_at** columns.

```
y2023_F$ride_length <- as.numeric(difftime(y2023_F$ended_at,y2023_F$started_at))
```

Once it gave the correct result, I applied it to all the rows and then used the **drop_na()** function so I could remove the null values. In total, 7,288 rows were eliminated. In this way, a total of 5,712,589 rows remained.
##Analyze data

```
y2023_F <- drop_na(y2023_F)
```

## Analyze data
Let's next focus on the average and the median lenght of each type of user. **ride_length** data was not converted to minutes, so we applied an operator of 60.

```
aggregate(y2023_F$ride_length/60 ~ y2023_F$member_casual, FUN = mean)
aggregate(y2023_F$ride_length/60 ~ y2023_F$member_casual, FUN = median)

```

Then I want to see the most common day of renting bykes by type of member.

```
aggregate(y2023_F$day_of_week ~ y2023_F$member_casual, FUN = mfv)
```

Now, we can see the average ride time by day for members and casual riders. Casual users ride longer than member, regardless of the day of the week.

```
options(warning=-1)
y2023_F %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, weekday) %>%  
  summarise(number_of_rides = n()  
            ,average_duration = mean(ride_length/60)) %>% 		
  arrange(member_casual, weekday)
```

## Create visualizations

I'll share the codes of each visualization:

> 1. Cyclistic_Plot_1
```
y2023_F %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Table 1: Number of Rides by Day and Rider Type") + 
  ylab("Number of Rides (1e+05 = 100,000)") + 
  xlab("Day of Week")
```
> 1. Cyclistic_Plot_1 | Number of rides by user's type
```
y2023_F %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length/60)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Table 2: Average Ride Duration by Day and Rider Type") + 
  ylab("Average Duration (minutes)") + 
  xlab("Day of Week")
```
> 2. Cyclistic_Plot_2 | Average duration
```
y2023_F %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>% 
  group_by(member_casual, weekday) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length/60)) %>% 
  arrange(member_casual, weekday)  %>% 
  ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Table 2: Average Ride Duration by Day and Rider Type") + 
  ylab("Average Duration (minutes)") + 
  xlab("Day of Week")
```
> 3. Cyclistic_Plot_3 | Rides by byke's type and user's type
```
y2023_F %>% 
  group_by(member_casual, rideable_type) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, rideable_type)  %>% 
  ggplot(aes(x = rideable_type, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") + 
  labs(title = "Table 3: Number of Rides by Bike Type and Rider Type") + 
  ylab("Number of Rides (5e+05 = 500,000)") + 
  xlab("Bike Type")
```
> 4. Cyclistic_Plot_4 | Rides by month and rider's type
```
y2023_F %>% 
  group_by(member_casual, month) %>% 
  summarise(number_of_rides = n()
            ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, month)  %>% 
  ggplot(aes(x = month, y = number_of_rides, group = member_casual)) +
  geom_line(aes(color = member_casual)) + 
  geom_point() +
  labs(title = "Table 6: Number of Rides by Month and Rider Type") + 
  ylab("Number of Rides (1e+05 = 100,000)") + 
  xlab("Month")
```
If you want, you can see the graphics [here](https://docs.google.com/presentation/d/e/2PACX-1vQtWqGkmwRpecQpDiyjC4W-jyOcUlWy0fEKUMr-RkgFUNI-Q0_o1wHKSsAf-6n0SIFhhNdCJGCsixbx/pub?start=false&loop=false&delayms=3000).

## Findings

According to the graphics and the study:
- On average, each trip is around 16 minutes: Casual users ride around 20 minutes; Members ride around 12 minutes
- Casual users use the app for longer trips than the members
- Regardless of being casual or member, the most popular day to rent a bike is Sunday
- Member rents more bikes than casuals, regardless the day of the week
- Casual riders use the service on weekend
- Casual users ride for longer than members
- The months of most use are in summer: July (7) and August (8)
- Members use more electric bikes than classic ones

## Time to act

After all this journey, the recommendations based on the analysis that can be made are:

- The majority of campaigns that are going to be carried out must be during the summer, since they are the months of greatest use
- Some kind of "Weekend Plan" could be created to offer an exclusive membership for Saturday and Sunday, so that some casual customers can take advantage of the membership
- Promote the use of electric bicycles for longer journeys through specific discounts

## Thanks

It's my first project with R, so thank you very much for coming this far. Any advice is welcome.
