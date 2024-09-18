---
title: Exploring the NoSQL Universe on my GitHub Repositories
author: vivian
date: 2024-09-17 11:33:00 +0800
categories: [Project_Profilo, IoT, Spark]
tags: []
pin: true
math: true
mermaid: true
image:
  path: /assets/img/lab3-5.png  
---
##  Exploring the NoSQL and Big Data Universe on my GitHub Repositories


```python 
from pyspark.sql import SparkSession

# Step 1: Initialize Spark session
spark = SparkSession.builder.appName("TotalWordCount").getOrCreate()

# Step 2: Load the text file into an RDD
textFile = spark.sparkContext.textFile("lab3-iot-gendata.txt")

# Step 3: Perform the word count
total_words = (
    textFile.flatMap(lambda line: line.split())  # Split lines into words
            .count()                            # Count the total number of words
)

# Step 4: Display the total number of words
print(f"Total number of words: {total_words}")

# Step 5: Stop the Spark session
spark.stop()

```