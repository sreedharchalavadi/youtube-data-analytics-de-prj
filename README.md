# Youtube data analytics ETL project

### Overview
This project was created to securely manage, streamline, and perform analysis on the structured and semi-structured YouTube videos data based on the video categories and the trending metrics.

### Project Goals
1. Data Ingestion — Build a mechanism to ingest data from different sources.
2. ETL System — We are getting data in raw format, transforming this data into the proper format.
3. Data lake — We will be getting data from multiple sources so we need centralized repo to store them.
4. Scalability — As the size of our data increases, we need to make sure our system scales with it.
5. Cloud — We can’t process vast amounts of data on our local computer so we need to use the cloud, in this case, we will use AWS.
6. Reporting — Build a dashboard to get answers to the question we asked earlier.

### Architecture
![Architecture diagram](https://github.com/sreedharchalavadi/youtube-data-analytics-de-prj/blob/main/architecture.jpeg)

### Services used

1. **AWS S3 (Simple Storage Service) :** Its an object storage service that provides manufacturing scalability, data availability, security, and performance. Its a highly scalable object storage service that can store and retrieve any amount of data from anywhere on the web.It is commonly used to store and distribute large media files, data backups and static website files.
2. **AWS IAM :** This is nothing but identity and access management which enables us to manage access to AWS services and resources securely.
3. **AWS Glue :** A serverless data integration service that makes it easy to discover, prepare, and combine data for analytics, machine learning, and application development.
4. **AWS Glue Crawler :** AWS Glue Crawler is a fully managed service that automatically crawls the data sources, identifies data formats and infers schemas to create an AWS Glue Data Catalog.
5. **AWS Glue Data Catalog :** AWS Glue Data Catalog is a fully managed metadata repository that makes it easy to discover and manage data in AWS. We can use the Glue Data Catalog with other AWS services, such as Athena
6. **AWS Lambda :** Lambda is a computing service that allows programmers to run code without creating or managing servers.We can use lambda to run code in response to events like changes in s3, DynamoDB, or other AWS services
7. **AWS Wrangler Layer :** A custom AWS wrangler layer zip file was downloaded and uploaded. This is a specialized Lambda Layer that includes the library, which provides an easy-to-use interface for working with data on AWS. It simplifies data engineering tasks by providing convenient methods for reading, writing, and transforming data in various formats and sources within AWS Lambda functions.
8. **AWS Athena :** Athena is an interactive query service that makes it easy to analyze data in amazon s3 using standard SQL. We can use Athena to analyze data in Glue Data catalog or in other s3 buckets
9. **AWS Cloud Watch :** Amazon cloudwatch is a monitoring service for AWS resources and the applications that run on them. We can use CloudWatch to collect and track metrics, collect and monitor log files and set alarms.
10. **AWS Glue Studio ETL (Extract, Transform, Load) :** These jobs are a visual interface within AWS Glue that enables users to design and build data transformation workflows without writing code. It simplifies the process of data extraction, transformation, and loading by providing a drag-and-drop interface for creating and managing ETL pipelines.
11. **QuickSight :** Amazon QuickSight is a scalable, serverless, embeddable, machine learning-powered business intelligence (BI) service built for the cloud.

### Dataset Used
This Kaggle dataset contains statistics (CSV files) on daily popular YouTube videos over the course of many months. There are up to 200 trending videos published every day for many locations. The data for each region is in its own file. The video title, channel title, publication time, tags, views, likes and dislikes, description, and comment count are among the items included in the data. A category_id field, which differs by area, is also included in the JSON file linked to the region.

This contains the information about the youtube dataset used in this project.
[Youtube Kaggle dataset](https://www.kaggle.com/datasets/datasnaek/youtube-new)

## Project Execution Flow

#### There are 2 set of files in Kaggle
a. Json files based on the region : This contains the category ids and their description for the videos present in the csv files.
b. csv files based on the region : This contains the information of the individual videos like the video title, channel title, publish time, tags, views, likes and dislikes, description, and comment count.

#### Goal is to identify the trending video categories from the dataset based on different regions. So that we can target our audiences accordingly.  

#### Steps Executed

1. created s3 buckets for the raw layer and loaded the data into the below folders using aws cli commands.
raw layer having category information data in json: s3://sree-de-on-youtube-raw-dev/youtube/raw_statistics_reference_data/
raw layer having video information region wise data in csv : s3://sree-de-on-youtube-raw-dev/youtube/raw_statistics/region=ca/

2. We need to create the lambda function to cleanse this json data and convert into parquet files (rows and columns). 
Further store this in the cleaned buckets and create a Glue catalog tables on top of this bucket using the crawler.

3. In lambda code, Dataframes are used to extract the items part from the json file. 
AWS data wrangler layer library is used to transform this json data into parquet format and store the files into cleaned s3 bucket. 
Bucket and path : s3://sree-de-on-youtube-cleansed-dev/youtube.
Glue catalog tables were created on top of the above s3 path : db_youtube_cleaned.cleaned_statistics_reference_data
Added the s3 trigger, so whenever there are new json files in the raw layer lambda code will run and store the data into above cleaned buckets.

4. Created a glue studio etl job to transform the csv files in the raw layer to the cleaned layer.
Retained the datatypes of big int in csv to bigint as it is in the cleaned parquet files.

5. Modified the pyspark code in etl to store the data in the cleaned layer with region as a partitioned columns.
Bucket : s3://sree-de-on-youtube-cleansed-dev/youtube/raw_statistics/ --> region wise data
Used predicate pushdown to filter out the fewer regions.

6. Created the glue crawler on s3://sree-de-on-youtube-cleansed-dev/youtube/raw_statistics/ --> region wise data and ran it. 
This will create the glue catalog tables on the raws statistics data in the cleaned bucket --> db_youtube_cleaned.raw_statistics.

7. Query the above cleaned tables using Athena.
    
    To get the raw data
    SELECT a.title, a.category_id, b.snippet_title FROM "de_youtube_raw"."raw_statistics" a
    inner join "db_youtube_cleaned"."cleaned_statistics_reference_data" b on a.category_id=b.id where a.region ='ca' limit 5;
    
    To get the cleaned data
    SELECT a.title, a.category_id, b.snippet_title FROM "db_youtube_cleaned"."raw_statistics" a
    inner join "db_youtube_cleaned"."cleaned_statistics_reference_data" b on a.category_id=b.id where a.region ='ca' limit 5;

8. Created the final Reporting layer with the ETL pipeline using AWS glue studio--> Created a job using visual with a source and target.
Joined the two tables from the cleaned layer as mentioned in the above query and store the results in the new bucket: sree-de-on-youtube-analytics-dev.
Also we need to create a analytics table on top of this final layer :  db_youtube_analytics.final_analytics. 
Run this ETL job which will create a new table on top of the analytics db with region as partitioned column.

9. Now query the results using the athena.
SELECT * FROM "db_youtube_analytics"."final_analytics" limit 10;

10. The final layer will be given to the data scientists so that they can run this simple query and create some Machine learning modules.

11. quicksight was used to create the dataset on top of the final analytics table : db_youtube_analytics.final_analytics with data source as s3 :sree-de-on-youtube-analytics-dev. 
We can create multiple analysis on top of this dataset and display them in various chart.
