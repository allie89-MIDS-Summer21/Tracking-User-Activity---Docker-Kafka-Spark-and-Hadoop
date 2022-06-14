# Business Questions
 
## To help answer a few of the business questions, we first need to put the extracted data in a temporary sql table.
```
data_clean.registerTempTable('df')
```
## Now we can run queries to answer:

#### Q1) How many assesstments are in the dataset? 
      -It seems like there are total of 3280 assessments.
   
Query:
```
spark.sql("select count(keen_id) from df").show()
```
Query Expected Output:
```
+--------------+
|count(keen_id)|
+--------------+
|          3280|
+--------------+
```

#### Q2) How many people took Learning Git? 
      -394 people took Learning Git

Query:
```
spark.sql("select count(*) from df where exam_name == 'Learning Git'").show()
```

Query Expected Output:
```
+--------+
|count(1)|
+--------+
|     394|
+--------+
```
#### Q3) What is the least common course taken? And the most common?
      - The least common course is Learning to iVsualize and the most common is Learning Git.
  
Query:
```
spark.sql("select exam_name, count(exam_name)  from df group by exam_name order by count(exam_name) desc").show(1)
```

Query Expected Output:
```
+------------+----------------+                                                 
|   exam_name|count(exam_name)|
+------------+----------------+
|Learning Git|             394|
+------------+----------------+
```

Query:
```
spark.sql("select exam_name, count(exam_name)  from df group by exam_name order by count(exam_name)").show(1)
```

Query Expected Output:
```
+--------------------+----------------+
|           exam_name|count(exam_name)|
+--------------------+----------------+
|Learning to Visua...|               1|
+--------------------+----------------+
```

#### Q4) How many different assessments are there? 
      - There are total of 103 unique assessments.

Query:
```  
spark.sql("select count(distinct(exam_name)) from df").show()
```
Query Expected Output:
```
|count(DISTINCT exam_name)|
+-------------------------+
|                      103|
+-------------------------+
```

## end pyspark
```
quit()
```