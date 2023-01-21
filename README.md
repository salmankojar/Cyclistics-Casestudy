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
