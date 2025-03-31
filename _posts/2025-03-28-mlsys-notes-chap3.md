---
title: "Designing ML Systems Notes: Chapter 3"
date: 2025-03-31
categories: [System Design, ML Notes]
tags: [system design, machine learning, data engineering]   
math: true
---

# Data Engineering Fundamentals

### Row-Major vs Column-Major Format

- CSV and Parquet are among the common methods of storing data. 
- CSV in a row-major, which mean consecutive elements in a row are stored next to each other in memory whereas Parquet is a column-major format, which means the consecutive elements in a column are stored next to each other in memory.
- In ML, if you want to access all the features of a sample at once, you need to use a row-major (CSV), but if you want to access all the samples of a feature at once, you need to use a column-major (Parquet).
- Row-major formats are better when you have to do a lot of writes, whereas column-major formats are better when you have to do a lot of column-based reads.
- One subtle point that a lot of people don't realize is that Pandas is a column-major format whereas NumPy is a row-major format by default. Accessing columns in Pandas *DataFrame* is fast but if you want to access its rows fast, you need to convert it to a *ndarray*.

<img src="/assets/img/row-col-major.png" style="width:600px;" alt="Task Interference">

## Data Storage Engines and Processing

- Storage engine, also databases, are the implementation of how data is stored and retrieved on machines.
- Usually, there are two types of workloads that databases are optimized for.

### Online Transactional Processing (OLTP)
-  Transaction refers to any kind of action: tweeting, ordering a ride through a ride-sharing app, uploading a new model, watching a YouTube video and so on.
- Transactions are inserted as they are generated and occasionally updates or deleted.
- Since they involve users, they need to be process fast (low latency) and have high availability.
- When people hear transactional database, they think ACID:
    - Atomicity: when one system fails, the whole transaction fails.
    - Consistency: all transactions must follow a predefined set of rules.
    - Isolation: two users accessing the same data at the same time should not interfere with each other.
    - Durability: once a transaction is committed, it should be stored permanently.
- These databases are often row-major, so they might not be efficient for questions such as "What's the average price for all the ride in September in San Francisco?"

### Online Analytical Processing (OLAP)
- OLAP is the opposite of OLTP. It is used for data analysis and reporting.


<br>

- OLAP and OLTP have become outdated for three main reasons"
    - Separation of transactional and analytical data
    - Storage and processing being coupled -- how data is stored in also how data in processed.
    - "Online" has been an overloaded term meaning a lot of things.

## ETL: Extract, Transform, and Load

- ETL is *extracting* data from different sources, *transforming* it into a format that is useful for analysis, and *loading* it into a data warehouse.
- In Extract phase, you extract data from all data sources, validate the data and reject the data that doesn't meet the requirements.
- In Transform phase, you transform the data into a format that is useful for analysis. This includes cleaning the data, aggregating the data, and joining the data.
- In Load phase, you load the data into a data warehouse. This is usually done in a batch process.
- With large amount of data being product, companies often asked the question: "Why not just store all data in a data lake so we don't have to deal with schema changes? Whichever application needs data can just pull out raw data from there and process it"
- This process if loading data into storage first then processing it late is sometimes called ELT (Extract, Load, Transform).
- The problem with this approach is that it is not efficient. You have to load all the data into the data lake and then process it, which can take a lot of time and resources.

## Batch Processing Versus Stream Processing
- Once data arrives in data storage engines, it is called historical data. Processing these historical data in batch is called batch processing.
- This is opposed to streaming data, which is data that is generated in real-time and needs to be processed in real-time.
- Stream processing can give low latency when done right buy many people believe it isn't as efficient as batch processing because it can leverage tools like MapReduce and Spark.
- That is not always the case since there are tools like Apache Flint which are highly scalable and fully distributed.
- In ML, batch processing is used to compute features that change less and those are called static features.
- Streaming processing is used to compute features that change more frequently and those are called dynamic features.

