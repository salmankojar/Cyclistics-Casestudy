# Cyclistics-Casestudy
Capstone Project for Google Data Analytics Certification
##Case Study: How Does a Bike-Share Navigate Speedy Success?

![bikeshare_Adobe](https://user-images.githubusercontent.com/123249434/213870425-c2facb6f-98b2-4a82-a824-a85ab5841fc1.jpg)


## Introduction

Welcome to the Cyclistic bike-share  analysis case study! In this case study, I will perform many real-world tasks of a junior data analyst. I am working for a ﬁctional company, Cyclistic, and met diﬀerent characters and team members. In order to answer the key business questions, i am following the steps of the data analysis process: ask, prepare, process, analyze, share, and act.

## Scenario

I am a junior data analyst working in the marketing analyst team at Cyclistic, a bike-share  company in Chicago. The director of marketing believes the company’s future success depends  on maximizing the number of annual memberships. Therefore, my team wants to understand  how casual riders and annual members use Cyclistic bikes diﬀerently. From these  insights, my team will design a new marketing strategy to convert casual riders into annual members. But ﬁrst, Cyclistic executives must approve my recommendations, so they must be backed up with compelling data insights and professional data visualizations.

## Characters and teams

●	Cyclistic: A bike-share  program that features more than 5,800  bicycles and 600 docking stations.  Cyclistic sets itself apart by also oﬀering reclining bikes, hand tricycles, and cargo bikes, making bike-share  more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use them to commute  to work each day.

●	Lily Moreno:  The director of marketing and your manager.  Moreno is responsible for the development  of campaigns and initiatives to promote  the bike-share  program. These may include email, social media, and other channels.

●	Cyclistic marketing analytics team: A team of data analysts who are responsible for collecting, analyzing, and reporting data that helps guide Cyclistic marketing strategy. You joined this team six months ago and have been busy learning about Cyclistic’s mission and business  goals — as well as how you, as a junior data analyst, can help Cyclistic achieve them.

●	Cyclistic executive team: The notoriously detail-oriented executive team will decide whether to approve the recommended marketing program. 

## ASK -> Business Task

Identify how does causal users use cyclistics bike differently from Annual members so that proper advertisement and marketing plans can be made to convert casual members to Annual Members. 

## PREPARE

I will use Cyclistic’s historical trip data to analyze and identify trends.  Downloaded 12 months of Cyclistic trip data from here [CLICK HERE](https://divvy-tripdata.s3.amazonaws.com/index.html) (Note: The datasets have a diﬀerent name because Cyclistic is a ﬁctional company. For the purposes  of this case study, the datasets are appropriate  and will enable  you to answer the business  questions. The data has been made available by Motivate International  Inc. under this  license.) This is public data that you can use to explore how diﬀerent customer types are using Cyclistic bikes. But note that data-privacy issues prohibit you from using riders’ personally identiﬁable information.  This means  that you won’t be able to connect  pass purchases to credit card numbers  to determine if casual riders live in the Cyclistic service area or if they have purchased  multiple single passes.

## PROCESS

I have downloaded 12 months data file from jan 2022 to Dec 2022 into .csv format.Since this data has many records, i have used MYSQL workbench for further data merging and data cleaning Process.

-- creating table to load csv data for each month into different tables
```
CREATE TABLE jan (
		ride_id varchar(255),
		rideable_type varchar(255),
		started_at datetime,
		ended_at datetime,
		start_station_name varchar(255) default NULL,
		start_station_id double default NULL,
		end_station_name varchar(255) default NULL,
		end_station_id double default NULL,
		start_lat float default NULL,
		start_lng float default NULL,
		end_lat float default NULL,
		end_lng float default NULL,
		member_casual varchar(255)
) ;
```
importing csv file into newly created table for each month(Query shown for Only 1 table)
```
LOAD DATA LOCAL INFILE 'C:/ProgramData/MySQL/MySQL Server 8.0/Uploads/202212-divvy-tripdata.csv'
INTO TABLE jan
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```
creating table(trip_data) to load all months data in it
```
CREATE TABLE trip_data (
		ride_id varchar(255),
		rideable_type varchar(255),
		started_at datetime,
		ended_at datetime,
		start_station_name varchar(255) default NULL,
		start_station_id double default NULL,
		end_station_name varchar(255) default NULL,
		end_station_id double default NULL,
		start_lat float default NULL,
		start_lng float default NULL,
		end_lat float default NULL,
		end_lng float default NULL,
		member_casual varchar(255)
) 
```
merging all 12 months table in 1 single table named as trip_data
```
INSERT INTO trip_data
	SELECT * FROM jan
		UNION ALL
	SELECT * FROM feb
    UNION ALL
	SELECT * FROM mar
		UNION ALL
	SELECT * FROM aprl
    UNION ALL
	SELECT * FROM may
		UNION ALL
	SELECT * FROM jun
    UNION ALL
	SELECT * FROM jul
		UNION ALL
	SELECT * FROM aug
    UNION ALL
	SELECT * FROM sep
		UNION ALL
	SELECT * FROM oct
    UNION ALL
	SELECT * FROM nov
		UNION ALL
	SELECT * FROM dece;
```
counting total number of trips in whole year
```
SELECT 
    COUNT(ride_id) AS total_no_of_trips
FROM
    trip_data;
```
checked for duplicate rows but no duplicate rows found
```
SELECT 
    ride_id, COUNT(ride_id)
FROM
    trip_data
GROUP BY ride_id
HAVING COUNT(ride_id) > 1;
```
rename columns for proper understanding of data
```
ALTER TABLE trip_data
RENAME COLUMN rideable_type TO bike_type,
RENAME COLUMN started_at TO start_time,
RENAME COLUMN ended_at TO end_time,
RENAME COLUMN member_casual TO user_status;
```
added new column ride_lenghth_min to contain trip duration for each trip
```
ALTER TABLE trip_data
ADD COLUMN ride_length_min INTEGER;

UPDATE trip_data
SET ride_length_min = MINUTE(TIMEDIFF(end_time,start_time));
```
extracting day_of_week,month,year from start_time column and putting it into different columns
```
ALTER TABLE trip_data
ADD COLUMN day_of_week char(12),
ADD month_m char(12),
ADD year_y varchar(255);

UPDATE trip_data
SET day_of_week = DAYNAME(start_time);
UPDATE trip_data
SET month_m = MONTHNAME(start_time);
UPDATE trip_data
SET year_y = YEAR(start_time);
```
checked for different records in user_status if it contains values other then Causal And Member
```
SELECT 
    	 user_status
FROM
    	 trip_data
GROUP BY user_status;
```
Found many different records in user_status columnn other then Casual and Member.
hence substituted different entries with proper values
```
UPDATE trip_data
SET user_status = 'casual'
WHERE user_status LIKE '%casual%';

UPDATE trip_data
SET user_status = 'member'
WHERE user_status LIKE '%member%';
```
checked for spelling mistakes in bike_type column and no different records found
```
SELECT 
         bike_type
FROM
         trip_data
GROUP BY bike_type;
```
checked for records having start_time greater than end_time
```
SELECT 
      ride_id,
      start_time,
      end_time
FROM
      trip_data
WHERE
      start_time > end_time;
```
deleting few rows found from above query where start_time is greater than end_time 
```
DELETE FROM trip_data
WHERE start_time > end_time;
```
## PHASE 4 - ANALYZE

finding average, max and min value for ride_lenghth
```
SELECT	 AVG(ride_length_min),
	 MAX(ride_length_min),
         MIN(ride_length_min)
FROM
	 trip_data;
```
finding number of both type of riders
```
SELECT   user_status,
         COUNT(user_status) AS number_of_riders
FROM 
	 trip_data
GROUP BY 
	 user_status;
```
![Chart1](https://user-images.githubusercontent.com/123249434/213900986-0b46e010-426d-48c4-859f-041ea9a05afa.png)

finding average ride length for different user_status
```
SELECT	 user_status,
	 ROUND(AVG(ride_length_min)) AS average_ride_length
FROM
	 trip_data
GROUP BY
	 user_status;
```
![Chart8](https://user-images.githubusercontent.com/123249434/213901002-3f936049-8e90-49d3-8531-681d835cbd30.png)


finding average ride length for different user_status for different bike types
```
SELECT	 user_status,
	 bike_type,
         ROUND(AVG(ride_length_min)) AS average_ride_length
FROM
  	 trip_data
GROUP BY
       	 user_status,bike_type;
```    
![Chart2](https://user-images.githubusercontent.com/123249434/213900836-6dd1ef62-ba99-4541-9508-6e6ae1a154c7.png)

finding average ride length for different user_status on different day of week
```
SELECT 
   	 day_of_week,
    	 user_status,
    	 ROUND(AVG(ride_length_min)) AS average_ride_length
FROM
   	 trip_data
GROUP BY 
	 user_status , day_of_week
ORDER BY 
	 day_of_week,user_status;
```	 
![Chart3](https://user-images.githubusercontent.com/123249434/213900884-63e97df9-3ff9-4e47-9e73-e7571bb0e6a5.png)

finding average ride length for both user types on different months of year
```
SELECT 
  	 month_m,
    	 user_status,
    	 ROUND(AVG(ride_length_min)) AS average_ride_length
FROM
    	 trip_data
GROUP BY 
	 month_m,user_status
ORDER BY 
	 month_m;
```
![Chart4](https://user-images.githubusercontent.com/123249434/213900932-ca138270-8e6e-499e-b73a-111dfc7a8ed5.png)


finding bike type preferences for both user type
```
SELECT 
    	user_status,
    	bike_type, 
    	COUNT(bike_type) AS bike_type_count
FROM
    	trip_data
GROUP BY 
	user_status , bike_type
ORDER BY
	user_status;
```
finding number of trips for different day of week
```
SELECT
	user_status,
	day_of_week,
        COUNT(ride_id) AS num_of_rides
FROM
	trip_data
GROUP BY
	user_status,day_of_week
ORDER BY
	user_status,day_of_week;
```
![Chart5](https://user-images.githubusercontent.com/123249434/213901042-e7a5ffac-362b-4061-8240-ee1aa64fdd2e.png)

finding total number of rides on different day of week for different months of year
```
SELECT 
    	month_m,
    	day_of_week,
    	COUNT(ride_id) AS Number_of_rides
FROM
    	trip_data
GROUP BY
	month_m,day_of_week
ORDER BY 
	month_m,day_of_week;
```
![Chart7](https://user-images.githubusercontent.com/123249434/213901063-5b702b49-890f-448a-aac3-e1a219699e2a.png)

finding number of rides for different hours of the day on different day of week
```
SELECT 
	day_of_week,
    	EXTRACT(hour FROM start_time) AS hour_of_day,
    	COUNT(ride_id) AS num_of_rides
FROM
	trip_data
GROUP BY
	day_of_week,hour_of_day
ORDER BY
	day_of_week,hour_of_day;
```
![Chart6](https://user-images.githubusercontent.com/123249434/213901052-27a5cde0-d30f-4d38-be94-4756ff04c561.png)

## SHARE
 Link for my tableau dashboard for cyclistics case is [here](https://public.tableau.com/views/CyclisticsCasestudy/CyclisticsDashboard?:language=en-US&:display_count=n&:origin=viz_share_link)
### Key FIndings
After Creating visualisations of results found in ANALYZE Phase, I have found following key findings from them
1. Number of Member users are more than casual users but only by 10%.
2. Average ride length of Casual Users is much greater than Members.
3. All 3 different types of bikes are rided for long time by Casual users compared to members.
4. Members users ride for almost same time on all day of week As compared to Casual users who rides more on weekends.
5. Both Casual and Members rides more around summer season(March to July) Compared to different time of year
6. Both Casual and Members ride more in afternoon and evening time.
7. Saturday of july is busiest time of all year.

### Conclusions
From above point no 1 & 2 of Key findings we can conclude that casual users tend to ride bikes more for leisure trips over weekends while Annual Members use bike for moving from one point to another for daily activities such as going for work,going for gym,etc

## ACT 

### Recommendations
1. Create a different annual package for current CASUAL users having offers related to riding on WEEKENDS which will make them buy membership.
2. A special discount should be given to current Annual members so that it will encourage more causal users to buy Annual Subscription.
3. Interviews should be taken of current Annual Members about benefits of having Annual membership plan and then it should be posted on different social media posts,
4. Marketing and Advertisements should be done for limited period such as afternoon periods of weekends since more casual users rides more in that period. A proper Ad    Campaign or an special event to be done on saturday of july since it is busiest day of the year according to our data.This will avoid unncecessary cost in              advertisements. 
5. Create a new type of bikes more suitable for longer trips as compared to standard docked type bikes.This will make current casual users to buy annual membership to 	  enjoy new bikes for longer trips.
6. A special discounts should be given to current annual members if they convert one casual user to annual member.
