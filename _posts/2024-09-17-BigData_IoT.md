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
  path: /assets/img/lab3-5.png  
---

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