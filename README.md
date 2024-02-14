# **Cyclistic Case Study (Porfolio_1)**
 A case study focused on a bicycle rental company.
## Scenario
As a junior data analyst within the marketing analysis team at Cyclistic, a Chicago-based bike-share company, the director of marketing is convinced that the company's future prosperity hinges on optimizing the count of annual memberships. Consequently, our team is keen on unraveling the disparities in how casual riders and annual members utilize Cyclistic bikes. Armed with these findings, we aim to formulate a fresh marketing strategy tailored to transition casual riders into becoming annual members. However, it is imperative that our recommendations garner approval from Cyclistic executives, demanding the support of compelling data insights and professionally crafted data visualizations.
## Stakeholders
> 1. **Lily Moreno:** The responsibility for developing campaigns and initiatives to promote the bike-share program lies with both the director of marketing. 
> 2. **Cyclistic marketing analytics team:** A team of data analysts is entrusted with the task of collecting, analyzing, and reporting data to inform Cyclistic's marketing strategy.
> 3. **Cyclistic executive team:** A team known for its meticulous attention to detail will determine whether to endorse the proposed marketing program.
## Preparing the data
The study must be carried out with historical travel data in the last 12 months using Cyclistic, to analyze and identify trends. The repository can be found here.

I have used the data for the 12 months of 2023, as it is the most recent. At first, I decided not to delete any columns since there was no sensitive information (such as credit cards) and everything was anonymized.

The data is made up of 12 .csv each with 13 columns. In total, there are 5,712,861 rows of data.
##Tools
I have decided to use R for convenience. First of all, when managing such a large number of rows and columns at the same time, I ruled out using spreadsheets, as it could be time-consuming and I found it much less convenient.

My choice of R over Python comes from me feeling more comfortable in this ecosystem.

I have carried out the entire process in RStudio following guidelines and correcting errors.
## Transforming data
The first thing was to combine the 12 files into one so that I could work with all the data at the same time when necessary. I used the **bind_rows()** function to bind all the files together.

From that moment on, create a **head()** of the first 100 rows to be able to work faster and test code.

First, I checked the data type which were the **started_at** and **ended_at** columns, which were strings. Then I used the **asDate()** function to change them to dates.

From that change, I created four more columns where it was recorded: the day, the month, the year and the day of the week.

At the same time, I eliminated four columns that, in my opinion, were not going to be useful for the analysis: the latitudes and longitudes of the stations.

Next, I created a new column called **ride_length** to have the duration of each ride. I did this by applying the **difftime()** function inside **as.numeric** between the **ended_at** and **started_at** columns.

Once it gave the correct result, I applied it to all the rows and then used the **drop_na()** function so I could remove the null values. In total, 7,288 rows were eliminated. In this way, a total of 5,712,589 rows remained.
##Analyze data
