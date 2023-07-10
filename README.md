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
10. **QuickSight :** Amazon QuickSight is a scalable, serverless, embeddable, machine learning-powered business intelligence (BI) service built for the cloud.

### Dataset Used
This Kaggle dataset contains statistics (CSV files) on daily popular YouTube videos over the course of many months. There are up to 200 trending videos published every day for many locations. The data for each region is in its own file. The video title, channel title, publication time, tags, views, likes and dislikes, description, and comment count are among the items included in the data. A category_id field, which differs by area, is also included in the JSON file linked to the region.

This contains the information about the youtube dataset used in this project.
[Youtube Kaggle dataset](https://www.kaggle.com/datasets/datasnaek/youtube-new)
