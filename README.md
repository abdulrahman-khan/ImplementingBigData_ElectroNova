# Implementing Hadoop Systems Case Study: Revisited

A case study conducted by Abdulrahman Khan & Nathan Hill – December 2021
Revisited by Abdulrahman Khan – July 2024

**Full report document is available in the repository**

This report outlines the project requirements and provides a detailed analysis of the recommended solutions. It describes the processes implemented in the proof-of-concept solution and explains how these align with the company’s business needs.

---

## Executive Summary

This case study details the development and implementation of a proof-of-concept solution designed to meet ElectroNova’s Big Data storage and analysis needs. The solution leverages multiple technologies from the Hadoop ecosystem to deliver a scalable, high-performance data platform.

The report includes:

* Hardware specifications (disk space, server processing requirements)
* Software solutions integrated into the proof-of-concept
* Examples of data analysis performed using the implemented technologies

---
## Project Features

* **End-to-End Big Data Pipeline** – From data ingestion to analysis using multiple Hadoop ecosystem tools.
* **Multi-Technology Integration** – HDFS, MapReduce, Spark, Hive, Flume, and Sqoop working together.
* **Custom Data Onboarding** – Java-based preprocessing to organize and prepare files for upload.
* **Distributed Data Processing** – MapReduce and Spark jobs to perform large-scale computations.
* **SQL-Like Analysis with HiveQL** – Querying structured data and exporting results to MySQL.
* **Real-Time Data Ingestion** – Apache Flume configuration for handling smart meter data streams.
* **Data Export Automation** – Sqoop-based export from HDFS to relational databases.
* **Scalable System Design** – Hardware and software recommendations for future growth.

At a Glance: HDFS · MapReduce · Spark · Hive · Flume · Sqoop · Java · MySQL

---

## Data Onboarding

A simple Java program systematically organizes files into separate directories before uploading them to the server.

![image](https://github.com/user-attachments/assets/dd38cc86-4ec2-4059-bd18-d51263efed2e)

---

## Data Analysis: MapReduce

**Program files are located under the `Task2` project in this repository.**

**First Mapper & Reducer:**

* Collects energy consumption for a single day.
* Key: `(houseID, date, hour)`
* Reducer calculates hourly consumption by subtracting the previous interval’s cumulative reading (using `CustomWriter` to store the previous value).

CustomWriter.class

![image](https://github.com/user-attachments/assets/6860efb2-bd12-481a-8a98-cb7bedee394f)

DailyMapper1.java

![mapper1](https://github.com/user-attachments/assets/b1e339d0-a692-4769-9af9-387d4673bec7)

DailyReducer1.java

![reducer1](https://github.com/user-attachments/assets/01a270f6-96a6-46d8-b69a-f4cb9ac10af3)

Output

![output](https://github.com/user-attachments/assets/dd8b8a76-3d9e-42f4-862d-0ba1195c42d6)

**Second Mapper & Reducer:**

* Input: Output from the first MapReduce job
* Mapper key: `houseID`
* Reducer: Uses `Math.max()` to assign the maximum value to each key

DailyMapper2.java

![mapper2](https://github.com/user-attachments/assets/59a99178-94fb-4e00-9b2f-7e3485dc856c)

DailyReducer2.java

![reducer2](https://github.com/user-attachments/assets/37273cb5-3e28-4ec9-8c90-8be13eb38f14)

Output

![Output](https://github.com/user-attachments/assets/be03fbd9-afcd-4055-ae7f-5777d6392fb1)

---

## Data Analysis: HiveQL

**Creating Tables**

![image](https://github.com/user-attachments/assets/d6358181-9d25-475c-a109-7dc7b436c7ea)

**Populating Tables**

![image](https://github.com/user-attachments/assets/d2577226-8e99-45bf-b95f-567ea45d67ca)

**Average Consumption (Last 30 Days)**

1. House 1: 33,019.14
2. House 2: 9,063.07
3. House 12: 5,853.29
4. House 18: 7,562.65

![image](https://github.com/user-attachments/assets/e6a9748f-9df5-41f5-8ff6-cee73cb603d0)

**Total Electricity Consumption (Last 30 Days) – by Time of Day**

![image](https://github.com/user-attachments/assets/90500fcb-848b-4002-a988-6faf3440ffa9)

**Mid-Peak Consumption (7–11 AM & 5–7 PM)**

![image](https://github.com/user-attachments/assets/e81b309b-6656-4ce9-9e48-437b42d48c92)

**Evening Peak Consumption (5–7 PM)**

![image](https://github.com/user-attachments/assets/3db01d71-88ca-48c9-a628-05f622bd82c5)

---

## Data Analysis: Spark

![image](https://github.com/user-attachments/assets/19c007f6-03b9-4318-bb1e-342dcdae0b7f)
![image](https://github.com/user-attachments/assets/e57660f3-6d88-46e6-90ee-9d5b59948f6f)
![image](https://github.com/user-attachments/assets/0dbaca49-1b9d-4091-9b7a-185d03cd7553)
![image](https://github.com/user-attachments/assets/56d4c856-f133-439e-a317-45656f7e7fba)
![image](https://github.com/user-attachments/assets/5a678721-3516-4f6f-8a20-b6c769c1f7f0)

**Average Energy Consumed per House**

![image](https://github.com/user-attachments/assets/dc45b40e-0ed8-474c-b231-f30ebae2c62e)

---

## Data Pipelining: Flume Configuration

```properties
# Sources, sinks, and channels
agent1.sources = source1
agent1.sinks = sinkhdfs1 sinkhdfs2 sinkhdfs3
agent1.channels = channel1 channel2 channel3

# Source & sink mappings
agent1.sources.source1.channels = channel1 channel2 channel3
agent1.sinks.sinkhdfs1.channel = channel1
agent1.sinks.sinkhdfs2.channel = channel2
agent1.sinks.sinkhdfs3.channel = channel3

# Source settings
agent1.sources.source1.type = spooldir
agent1.sources.source1.spoolDir = /home/hadoopuser/group_project/flumeSource
...
```

**Running Flume**

![image](https://github.com/user-attachments/assets/d5903274-e5a5-4078-a085-78657e74c9c8)

**Flume Output Files**

![image](https://github.com/user-attachments/assets/1712b798-0e81-4eda-95ca-80eb407970ba)
![image](https://github.com/user-attachments/assets/738acb26-ea03-4676-9f3c-00b0c8e31a77)
![image](https://github.com/user-attachments/assets/286f473b-26fe-415f-aa62-0f19d7dbca3e)

---

## Data Pipelining: Sqoop Export

Export from HDFS to MySQL:

```bash
sqoop export \
  --connect jdbc:mysql://localhost/task5 \
  --username root \
  --password Sher1dan@ \
  --table energydata \
  --columns "LOGID","HOUSEID","CONDATE","CONHOUR","ENERGYREADING","FLAG" \
  --m 1 \
  --export-dir /group_project/YearMonth/2016/12 \
  --input-fields-terminated-by '\0011'
```

**Results:**

* 49,759 fields exported to MySQL

![image](https://github.com/user-attachments/assets/f02d5675-0b96-479b-94ca-a2d66a50ebab)

**MySQL Query Example**

![image](https://github.com/user-attachments/assets/8f023ea7-fad4-48d4-898d-104f7adeb5c0)

---

## Conclusion

Big Data technologies revolve around usability and scalability. Our proposed solution for ElectroNova demonstrates how these tools can provide actionable insights and support business growth.

**Technologies Used:**

* **HDFS:** Securely stores and manages large datasets
* **MapReduce:** Performs distributed, large-scale computations
* **Apache Spark:** Enables high-performance data analysis
* **Apache Hive:** Supports SQL-like queries and MySQL exports
* **Apache Flume:** Handles real-time ingestion from smart meters

The solution meets current requirements and is designed to scale for future needs.

---

## References

The dataset used was adapted from the HUE dataset. Hourly aggregate consumption logs were resampled at 10-second intervals for educational purposes only.

**Dataset Citation:**

* Stephen Makonin, 2019 – *HUE: The Hourly Usage of Energy Dataset for Buildings in British Columbia*, Data in Brief, vol. 23, no. 103744, pp. 1-4
* Makonin, Stephen, 2018 – *HUE Dataset*, Harvard Dataverse, V5, [https://doi.org/10.7910/DVN/N3HGRN](https://doi.org/10.7910/DVN/N3HGRN)

