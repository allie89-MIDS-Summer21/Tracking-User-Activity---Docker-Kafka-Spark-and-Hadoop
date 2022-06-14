# Step-by-Step Instructions to Run the Data Pipeline

### in your current directory, create a new directory you will be working in, mine is called project-2-allie89-W205
```
mkdir project-2-allie89-W205
```

### save the docker-compose yml file you will be using for the docker containers in this directory

### get the data
```
curl -L -o assessment-attempts-20180128-121051-nested.json https://goo.gl/ME6hjp
```
### start up docker
```
docker-compose up -d
```
### check what is running in our current docker, make sure all the containers specified in yml file are up
```
docker-compose ps
```
### create a topic assessment, as the data contains all assessments and should belong in the same topic. If you name your topic something else, make sure you update assessment to what you will be using in the line with "topic assessment"
```
docker-compose exec kafka \
  kafka-topics \
    --create \
    --topic assessment \
    --partitions 1 \
    --replication-factor 1 \
    --if-not-exists \
    --zookeeper zookeeper:32181
```

### check to make sure topic is created successfully
```
docker-compose exec kafka \
  kafka-topics \
  --describe \
  --topic assessment \
  --zookeeper zookeeper:32181
``` 

### publish test messages with kafkacat, make sure you adjust the file path based on your directory
```
docker-compose exec mids bash -c "cat /w205/project-2-allie89-W205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c | kafkacat -P -b kafka:29092 -t assessment && echo 'Produced 100 messages.'"
```

### adding some color to separate keys vs values, make sure you adjust the file paths based on your directory
```
docker-compose exec mids bash -c "cat /w205/project-2-allie89-W205/assessment-attempts-20180128-121051-nested.json | jq '.'"
```
```
docker-compose exec mids bash -c "cat /w205/project-2-allie89-W205/assessment-attempts-20180128-121051-nested.json | jq '.[]' -c"
```

### consume messages & print word count 
```
docker-compose exec mids bash -c "kafkacat -C -b kafka:29092 -t assessment -o beginning -e" | wc -l
```
    
### run pyspark
```docker-compose exec spark pyspark```

### read messages on kafka topic assessment with spark, save these messages as 'raw_assess'
```
raw_assess = spark \
  .read \
  .format("kafka") \
  .option("kafka.bootstrap.servers", "kafka:29092") \
  .option("subscribe","assessment") \
  .option("startingOffsets", "earliest") \
  .option("endingOffsets", "latest") \
  .load()
```  
### see the schema of the messages
```
raw_assess.printSchema()
``` 
### see the messages
```
raw_assess.show()
```
### cache messages to reduce warnings
```
raw_assess.cache()
```

### cast messages as strings and save it as 'data'
```
data = raw_assess.select(raw_assess.value.cast('string'))
```
### look at the new data
```
data.show()
data.printSchema()
```

### pull out first entry 
```
data.select('value').take(1)
```
### pull out first entry and extract its value
```
data.select('value').take(1)[0].value
```
### unrolling data with json
```import json
```
### pull out first message
```
first_assessment = json.loads(data.select('value').take(1)[0].value)
```
### take a look
```
first_assessment
```
### print an item from first message
```
print(first_assessment['exam_name'])
```

### write assessments data in current form to hdfs
```
data.write.parquet("/tmp/data")
```
### open a new terminal window and check results using the below code
```
docker-compose exec cloudera hadoop fs -ls /tmp/
```
### go back to pysark window use sys to deal with with unicode encoding
```import sys
sys.stdout = open(sys.stdout.fileno(), mode='w', encoding='utf8', buffering=1)
```

### unroll and save these extracted assessments -- using json 
```
extracted_data = data.rdd.map(lambda x: json.loads(x.value)).toDF()
```
### take a look at the data
```
extracted_data.show()
```
### convert extracted data to string
```
extracted_data_string = raw_assess.select(raw_assess.value.cast('string'))
```
### looking at the data unrolled version 
```
extracted_data_string.show()
```

### unroll extra nesting and save that as 'data_clean'
```
data_clean = spark.read.json(extracted_data_string.rdd.map(lambda x: (x.value))
```
### printing schema of the unrolled data
```
data_clean.printSchema()
```
### save this as a parquet file
```
data_clean.write.parquet("/tmp/data_clean")
```
### open a new terminal window and check if new clean data is saved
```
docker-compose exec cloudera hadoop fs -ls /tmp/
```
## ****************end of pipeline***************

### Get history of pyspark
```
docker-compose exec spark cat /root/.python_history > spark_history.txt
```

### Get history of console
```
history > allie89-W205-history.txt
```