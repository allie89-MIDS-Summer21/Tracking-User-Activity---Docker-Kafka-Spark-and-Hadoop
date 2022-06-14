#                                     Project 2: Tracking User Activity

## Summary:

The purpose of this project was to apply data engineering on the assessment delivery service to pull, publish, transform and flatten a very messy dataset. This in turn would allow data scientists to run queries and help answer their business questions. The tools that were used in this process were: Docker, Kafka, Spark, and Hadoop. Docker created the containers that allowed the pipeline work to take place. Kafka created a topic, in my case called assessment, which published and consumed the data in the topic. I chose assessment as my only topic because the data contains just exam information, I found it appropriate to file them all in one 'folder' called assessment, also known as the topic. Spark helped transform and read the data from Kafka. Last but not least, Hadoop was in charge of storing the data in a table format so that it can be used to query to help answer any business questions. Throughout the process, I did come across some challenges which included unrolling the multiple nested JSON data and reading messages from Kafka on pyspark, but a few lines of spark code helped overcome these issues.  




## Files in My Repo:

###  'data_pipeline.md' 
- Markdown file that contains step by step instructions on how to set up the data pipeline in order to successfully engineer the data and use it for business analysis.

###  'data_analysis.md'
- Markdown file that contains the data analysis portion in pyspark after the data has been sent to hdsf. The queries, respective outputs and the answers to business questions are also included.

###  'docker-compose.yml'
- file used to spin up docker



