# Implementing Hadoop Systems Case Study: Revisited
A case study conducted by Abdulrahman Khan & Nathan Hill in December 2021

Revisited by Abdulrahman Khan in July 2024

**Full report document can be found in the repository**

This report outlines the project requirements and provides a detailed analysis of the recommended solutions. It also describes the processes implemented in the proof-of-concept solution and how these align with the company's business needs.


**TODO: ADD ICONS FOR TECHNOLOGIES USED**

# Executive Summary
This case study details the development and implementation of a proof of concept solution designed to meet ElectroNova’s Big Data storage and analysis needs. Our team has crafted a cutting-edge solution by leveraging a range of technologies from the Hadoop ecosystem. This report covers the necessary hardware specifications, including required disk space and recommended server processing power for effective data analysis. Additionally, the report provides an in-depth look at the software solutions integrated into the proof of concept and includes examples of data analysis conducted using these technologies.

# Data Onboarding
Simple Java program to systematically organize files into separate directories before uploading them to the server

![image](https://github.com/user-attachments/assets/dd38cc86-4ec2-4059-bd18-d51263efed2e)

# Data Analysis: MapReduce
**The program files can be found under the Task2 project in this repository.**

The first Mapper and Reduce programs collect the Energy Consumption for a singular day. The key pair value is the houseID, the date and the hour at which the reading was taken.
The reducer combines the values to get the Energy Consumption for that singular hour. Since the readings are cumulative, we calculate the difference from the previous interval. 
We use a CustomWriter class to hold the previous reading's value.

CustomWriter.class

![image](https://github.com/user-attachments/assets/6860efb2-bd12-481a-8a98-cb7bedee394f)

DailyMapper1.java

![mapper1](https://github.com/user-attachments/assets/b1e339d0-a692-4769-9af9-387d4673bec7)

DailyReducer1.java

![reducer1](https://github.com/user-attachments/assets/01a270f6-96a6-46d8-b69a-f4cb9ac10af3)

Output

![output](https://github.com/user-attachments/assets/dd8b8a76-3d9e-42f4-862d-0ba1195c42d6)



The second Mapper and Reducer programs take the output from the first MapReduce program.
The mapper assigns a key pair value of houseID. The reducer compares the values and assigns the max value to the key value pair, using Math.max()

DailyMapper2.java

![mapper2](https://github.com/user-attachments/assets/59a99178-94fb-4e00-9b2f-7e3485dc856c)

DailyReducer2.java

![reducer2](https://github.com/user-attachments/assets/37273cb5-3e28-4ec9-8c90-8be13eb38f14)

Output

![Output](https://github.com/user-attachments/assets/be03fbd9-afcd-4055-ae7f-5777d6392fb1)

# Data Analysis: HiveQL Queries Analysis
Creating tables

![image](https://github.com/user-attachments/assets/d6358181-9d25-475c-a109-7dc7b436c7ea)

Populating Tables

![image](https://github.com/user-attachments/assets/d2577226-8e99-45bf-b95f-567ea45d67ca)


Average Consumption over the last 30 days
  1. House 1: 33019.14
  2. House 2: 9063.07
  3. House 12: 5853.29
  4. House 18: 7562.65

![image](https://github.com/user-attachments/assets/e6a9748f-9df5-41f5-8ff6-cee73cb603d0)


Total electricity consumption in the most recent 30 days by time of day

![image](https://github.com/user-attachments/assets/90500fcb-848b-4002-a988-6faf3440ffa9)

Consumption during mid peak 7am to 11am and 5pm to 7pm

![image](https://github.com/user-attachments/assets/e81b309b-6656-4ce9-9e48-437b42d48c92)

Consumption between 5pm to 7pm only

![image](https://github.com/user-attachments/assets/3db01d71-88ca-48c9-a628-05f622bd82c5)

# Data Analysis: Spark Analysis

![image](https://github.com/user-attachments/assets/19c007f6-03b9-4318-bb1e-342dcdae0b7f)


![image](https://github.com/user-attachments/assets/e57660f3-6d88-46e6-90ee-9d5b59948f6f)


![image](https://github.com/user-attachments/assets/0dbaca49-1b9d-4091-9b7a-185d03cd7553)


![image](https://github.com/user-attachments/assets/56d4c856-f133-439e-a317-45656f7e7fba)

![image](https://github.com/user-attachments/assets/5a678721-3516-4f6f-8a20-b6c769c1f7f0)

Average energy consumed per house

![image](https://github.com/user-attachments/assets/dc45b40e-0ed8-474c-b231-f30ebae2c62e)




# Data Pipelining: Flume Configuration

```
agent1.sources = source1
agent1.sinks = sinkhdfs1 sinkhdfs2 sinkhdfs3
agent1.channels = channel1 channel2 channel3

# LINK FOR SOURCE AND SINK
agent1.sources.source1.channels = channel1 channel2 channel3
agent1.sinks.sinkhdfs1.channel = channel1
agent1.sinks.sinkhdfs2.channel = channel2
agent1.sinks.sinkhdfs3.channel = channel3

# Define the source
agent1.sources.source1.type = spooldir
agent1.sources.source1.spoolDir = /home/hadoopuser/group_project/flumeSource

# Define interceptor to extract
agent1.sources.source1.interceptors = i1 i2
agent1.sources.source1.interceptors.i1.type = regex_extractor
agent1.sources.source1.interceptors.i2.type = regex_extractor
# timestamp
agent1.sources.source1.interceptors.i1.regex = ^?(\\d\\d\\d\\d-\\d\\d-\\d\\d)
agent1.sources.source1.interceptors.i1.serializers = tt
agent1.sources.source1.interceptors.i1.serializers.tt.type= org.apache.flume.interceptor.RegexExtractorInterceptorMillisSerializer
agent1.sources.source1.interceptors.i1.serializers.tt.name = timestamp
agent1.sources.source1.interceptors.i1.serializers.tt.pattern = yyyy-MM-dd
# flag
agent1.sources.source1.interceptors.i2.regex = (.{1}$)
agent1.sources.source1.interceptors.i2.serializers = flag
agent1.sources.source1.interceptors.i2.serializers.flag.name = flag

agent1.sources.source1.selector.type = multiplexing
agent1.sources.source1.selector.header = flag
agent1.sources.source1.selector.mapping.0 = channel1 channel2
agent1.sources.source1.selector.mapping.1 = channel3
# yyyymmddhhmmss
# consumption_6_20200101235850
# SINK 1 - YEAR MONTH
agent1.sinks.sinkhdfs1.type = hdfs
agent1.sinks.sinkhdfs1.hdfs.path = /group_project/YearMonth/%Y/%m
agent1.sinks.sinkhdfs1.hdfs.filePrefix = consumption
agent1.sinks.sinkhdfs1.hdfs.fileSuffix = .txt
agent1.sinks.sinkhdfs1.hdfs.rollInterval = 0
agent1.sinks.sinkhdfs1.hdfs.rollCount = 0
agent1.sinks.sinkhdfs1.hdfs.rollSize = 1000000
agent1.sinks.sinkhdfs1.hdfs.inUsePrefix = _
agent1.sinks.sinkhdfs1.hdfs.fileType = DataStream
# SINK 2 - YEAR MONTH DAY
agent1.sinks.sinkhdfs2.type = hdfs
agent1.sinks.sinkhdfs2.hdfs.path = /group_project/YearMonthDay/%Y/%m/%d
agent1.sinks.sinkhdfs2.hdfs.filePrefix = consumption
agent1.sinks.sinkhdfs2.hdfs.fileSuffix = .txt
agent1.sinks.sinkhdfs2.hdfs.rollInterval = 0
agent1.sinks.sinkhdfs2.hdfs.rollCount = 0
agent1.sinks.sinkhdfs2.hdfs.rollSize = 1000000
agent1.sinks.sinkhdfs2.hdfs.inUsePrefix = _
agent1.sinks.sinkhdfs2.hdfs.fileType = DataStream
# SINK - FLAGGED
agent1.sinks.sinkhdfs3.type = hdfs
agent1.sinks.sinkhdfs3.hdfs.path = group_project/Flagged
agent1.sinks.sinkhdfs3.hdfs.filePrefix = consumption
agent1.sinks.sinkhdfs3.hdfs.fileSuffix = .txt
agent1.sinks.sinkhdfs3.hdfs.rollInterval = 0
agent1.sinks.sinkhdfs3.hdfs.rollCount = 0
agent1.sinks.sinkhdfs3.hdfs.rollSize = 1000000
agent1.sinks.sinkhdfs3.hdfs.inUsePrefix = _
agent1.sinks.sinkhdfs3.hdfs.fileType = DataStream

agent1.channels.channel1.type = memory
agent1.channels.channel2.type = memory
agent1.channels.channel3.type = memory
```
Running Flume

![image](https://github.com/user-attachments/assets/e843572a-c3ac-4451-909a-09bfd4749856)

Flume Output Files

![image](https://github.com/user-attachments/assets/1712b798-0e81-4eda-95ca-80eb407970ba)

![image](https://github.com/user-attachments/assets/738acb26-ea03-4676-9f3c-00b0c8e31a77)

![image](https://github.com/user-attachments/assets/286f473b-26fe-415f-aa62-0f19d7dbca3e)

# Data Pipelining: Sqoop Export 
We then utilized Sqoop’s export functionality to export data from HDFS to MySQL by running this command
```
sqoop export --connect jdbc:mysql://localhost/task5 --username root --password Sher1dan@ --table energydata --columns "LOGID","HOUSEID","CONDATE","CONHOUR","ENERGYREADING","FLAG" --m 1 --export-dir /group_project/YearMonth/2016/12 --input-fields-terminated-by '\0011'
```

49,759 fields have been exported to our MySQL table

![image](https://github.com/user-attachments/assets/f02d5675-0b96-479b-94ca-a2d66a50ebab)

Running a MySQL query

![image](https://github.com/user-attachments/assets/8f023ea7-fad4-48d4-898d-104f7adeb5c0)


# Conclusion and Summary
Big Data Technologies fundamentally revolve around usability and scalability. Our proposed solution for ElectroNova details how the company can leverage these technologies to derive actionable insights and drive business growth. The solution encompasses the physical hardware requirements, including calculations for necessary storage space and processing power to handle the data analysis efficiently.

We have integrated several technologies from the Hadoop ecosystem to enable versatile data analysis. This includes:
  - Hadoop Distributed File System (HDFS): Utilized to securely store and manage the company’s extensive database
  - MapReduce: Implemented to facilitate rapid and complex analysis across the entire dataset
  - Apache Spark: Provides an alternative for running sophisticated data analyses with enhanced performance
  - Apache Hive: Allows for HiveQL queries and supports data export for MySQL queries, offering flexibility in data manipulation
  - Apache Flume: Designed to handle real-time data from smart meters, ensuring timely and accurate data processing.
    
Our solution addresses ElectroNova’s need for quick, intelligent, and efficient data processing. It is tailored to meet the company’s current requirements and is scalable to accommodate future growth.


# References
The sample data used in this project is modified from the HUE dataset. The modifications made include changing the hourly aggregate consumption log to sampling at 10-second intervals. The hourly consumption was split equally across 10-second intervals within the hour. The modified data will only be used for educational purposes and is not intended to be used for any real life research work.

Dataset reference:
Stephen Makonin, 2019,  “HUE: The Hourly Usage of Energy Dataset for Buildings in British Columbia,” Data in Brief, vol. 23, no. 103744, pp. 1-4 (2019).

Makonin, Stephen, 2018, "HUE: The Hourly Usage of Energy Dataset for Buildings in British Columbia", https://doi.org/10.7910/DVN/N3HGRN, Harvard Dataverse, V5, UNF:6:F2ursIn9woKDFzaliyA5EA== [fileUNF]











