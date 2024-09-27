---
title: Exploring the Big Data and IoT
author: vivian
date: 2024-09-17 11:33:00 +0800
categories: [Project_Profilo, IoT, Spark]
tags: []
pin: true
math: true
mermaid: true
image:
  path: /assets/img/spark.png  
---


## PySpark Distributed Processing -- Counting Words: 


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

## split() vs split(' ')
Use split() if you want to split by any amount of whitespace and ignore extra spaces.
Use split(' ') if you need to preserve the exact spacing, including empty strings where multiple spaces exist.

## map() vs flatMap():
map(): Transforms each input element to exactly one output element or one collection. The output retains the nested structure if the transformation returns a collection.
flatMap(): Transforms each input element to zero, one, or more elements and flattens the result into a single collection.

```python 
rdd = sc.parallelize(["Hello world", "How are you"])
# Using map (each line becomes a list of words)
mapped = rdd.map(lambda line: line.split())
print(mapped.collect())  
# Output: [['Hello', 'world'], ['How', 'are', 'you']]

# Using flatMap (each line is split into words, but the lists are flattened into a single collection)
flat_mapped = rdd.flatMap(lambda line: line.split())
print(flat_mapped.collect())  
# Output: ['Hello', 'world', 'How', 'are', 'you']


```

PySpark's Distributed Processing Mechanism
PySpark is the Python API for Apache Spark, a distributed computing framework. Spark provides a powerful engine for processing large datasets in parallel across a cluster of machines, or even on a single machine with multiple CPU cores. Here's how PySpark enables distributed processing:

1. Resilient Distributed Dataset (RDD)
PySpark's core abstraction for distributed data processing is the Resilient Distributed Dataset (RDD). An RDD is a fault-tolerant collection of elements that can be operated on in parallel. When you load data into an RDD, PySpark automatically splits the data into partitions and distributes those partitions across the available cluster nodes or CPU cores.

Each partition can be processed independently in parallel, enabling efficient large-scale data processing.

2. Parallel Operations
PySpark provides a wide range of operations (such as map(), filter(), flatMap(), reduceByKey(), etc.) that are executed in parallel across the partitions of the RDD. These operations do not need to be explicitly parallelized; they are automatically distributed across the available nodes or cores by PySpark.

For example, if you load a large text file into an RDD and apply a transformation like flatMap(), each line of the file is processed independently across the different partitions. This allows PySpark to work on multiple parts of the data simultaneously.

3. Lazy Evaluation
PySpark employs lazy evaluation, meaning that transformations on RDDs (like map(), flatMap(), and filter()) are not executed immediately. Instead, they are recorded as a lineage of operations. When an action (such as collect(), count(), or saveAsTextFile()) is called, Spark triggers the actual computation by executing the recorded transformations in a distributed fashion. This lazy evaluation optimizes the execution plan by minimizing data movement and recomputing only when necessary.

4. Distributed Storage
When you load a large dataset into PySpark, the data can be stored on distributed file systems like HDFS (Hadoop Distributed File System) or S3 (Amazon Simple Storage Service). Spark reads the data in parallel from these distributed storage systems, allowing it to process massive datasets that cannot fit into memory on a single machine.

## Map Reduce
1. Map Phase: This phase processes the input data and produces intermediate key-value pairs.
2. Reduce Phase: This phase takes the intermediate key-value pairs produced by the Map phase and aggregates or reduces them to produce the final output. 


```python 
import sys
from operator import add
from pyspark.sql import SparkSession

# Initialize the Spark session
spark = SparkSession.builder.appName("wordCount").getOrCreate()

# Load the file as an RDD
textFile = spark.sparkContext.textFile("lab3-iot-gendata.txt") 

# Output the filtered histogram, grouping the numbers into two groups
counts = textFile.flatMap(lambda x: x.split()) \
    .filter(lambda x: x.isdigit()) \
    .map(lambda x: ('Group 1' if int(x) <= 50 else 'Group 2', 1)) \
    .reduceByKey(add)

# Collect and print the output
output = counts.collect()
for (group, count) in output:
    print(f"{group}: {count}")
```
```
output : 
Group 1: 43
Group 2: 57
```

## Use OpenSSL to Generate a Public Key
```bash 
$ cd ~
$ openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
$ openssl rsa -pubout -in private_key.pem -out public_key.pem
$ cat public_key.pem 
```

## Use openssl to encrypt data
```bash 
$ echo "Hello IoT" > iot-data.txt
$ openssl aes-256-cbc -md sha512 -pbkdf2 -iter 1000 -a -salt -in iot-data.txt -out iot-data.txt.enc
Password: abc123
Verifying...: abc123
$ cat iot-data.txt.enc
```

## Use openssl to decrypt data
```bash 
$ openssl aes-256-cbc -md sha512 -pbkdf2 -iter 1000 -d -a -in iot-data.txt.enc -pass pass:abc123
$ echo "abc123" > passwdfile
$ openssl aes-256-cbc -md sha512 -pbkdf2 -iter 1000 -d -a -in iot-data.txt.enc -pass file:./passwdfile
```

