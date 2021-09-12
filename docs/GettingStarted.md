
# **Introduction**

## **Why Spark?**

Hadoop was the first version of distributed computing

Issues with HDFS

  - Hard to manage and administer
  - Batch processing using Map Reduce
  - Large Datasets were written to disk for subsequent stages of operation which meant high i/o.
  - OK for batch processing but was slow while doing Machine learning / Streaming

Spark was created in order to overcome these obstacles .

[Apache Spark](https://spark.apache.org/) is a unified engine designed for large-scale distributed data processing, on premises in data centers or in the cloud.

Spark provides in-memory storage for intermediate computations, making it much faster than Hadoop Map Reduce. It incorporates libraries with composable APIs for machine learning (MLlib), SQL for interactive queries (Spark SQL), stream processing (Structured Streaming) for interacting with real-time data, and graph processing (GraphX).

Spark’s design philosophy centers around four key characteristics:

**Speed**
-   The Spark framework is optimized to benefit from the cheap commodity hardware nowadays (CPU / RAM)
-   Spark builds its query as Directed acrylic graph (DAG) which constructs an efficient computational graph that can be decomposed into tasks and then can be executed in parallel across the workers in cluster .
-   As data is retained in memory with limited disk i/o it has a huge performance boost .

**Ease of use**
-   All the high level abstractions such as data frames/datasets are built on top of simple logical structure called RDD.
-   This eventually leads to a simple programming model .

**Modularity**
-   Supports Scala/Java/Python/R
-   All the libraries are well documented and are unified
-   We can write a single application to do ML/Streaming in one go .

**Extensibility**
-   Focuses on parallel computation rather than storage
-   Decouple storage and computation
-   Supports many data sources and targets Apache Hadoop, Apache Cassandra, Apache HBase, MongoDB, Apache Hive, RDBMSs, and more—and process it all in memory.
-   The community of Spark developers maintains a list of [third-party Spark packages](https://oreil.ly/2tIVP) as part of the growing ecosystem

## **Apache Spark Components as a Unified Stack**

![](/docs/GettinsStarted/overview.png)


Spark has Four major components :

Each of these components is separate in spark core engine So in whichever language you write the code Python/R the core decomposes it into highly compact byte code that is executed in worker's JVM across the cluster .

**Spark SQL:**

-   Works Well with Structured Data (RDBMS, csv, parquet , AVRO, ORC)
-   Used to Read Data from RDBMS or structured data (csv, text, json etc) and create permanent / temporary tables in spark.
-   Can be used to read data from dataframe .
-   Useful to run SQL type queries

**Spark MLib**

-   Mlib provides many popular ML algorithms built on top of high level DataFrame based API to build models .
-   These API allow to extract or transform features , build pipelines and persist models during deployment .

**Spark Structured Streaming**

-   Continuous streaming model where a stream of continuous streaming data can be consumed .
-   Developers can treat these streams as tables and query them.
-   Used to combine and react in real time to both static data and streaming data from engines like Apache Kafka , kinesis and other data sources .

**GraphX**

-   Library for manipulating Graphs and perform graph parallel computations .

## **Apache Spark’s Distributed Execution Model**

(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image002.png)

**Spark Driver :**

-   Instantiates a spark Session
-   It Communicates with cluster manager , requests resources from Cluster Manager for spark execution (JVM) , transforms spark operations into DAG, schedules operations and co-ordinates with executors .

**Spark Session**

-   Uniform conduit for all spark operations and data
-   Used for creating JVM parameters , define Dataframe , Datasets , read data sources , access catalog metadata and issues SQL queries .
-   Entry point for all Spark functionality
-   Can be accessed using global variable spark or using sc .

**Cluster Manager**

-   Responsible for managing and allocating resources for the cluster
-   Spark supports 4 cluster managers

-   In built Standalone
-   Apache Hadoop Yarn
-   Apache Mesos
-   Kubernetes
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image003.png)

**Spark Executor**

-   Runs on each worker node in the cluster
-   Communicates with driver program and execute tasks on the worker nodes

## **Distributed Data and Partitions**

-   Actual Physical data is stored as partitions
-   Spark treats each partition as a dataframe in memory
-   Spark Executor reads the data from its closest partition
-   Partitioning allows parallelism as Each core in partition is assigned to its own data partition to work with .

(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image004.png)

Partitioning allows efficient parallelism . In distributed environment spark executor reads data from the nearest partition allowing efficient parallelism .
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image005.png)

For example, this code snippet will break up the physical data stored across clusters into eight partitions, and each executor will get one or more partitions to read into its memory:

log_df = spark.read.text("path_to_large_text_file").repartition(8)  
print(log_df.rdd.getNumPartitions())

## **Understanding Spark Application Concepts**

Key terminologies

-   Application : User Program consisting of driver and spark session .
-   Spark Session : Entry point to interact with Spark functionality .
-   Job : Parallel Computation consisting of many tasks
-   Stage : Each Job is divided into smaller tasks called stages
-   Task : Single unit of work

**Spark Application and Spark Session**
At the core of Spark Application is the spark driver program which creates a SparkSession object .
While working with an interactive shell Spark session is created automatically
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image006.png)

**Spark Jobs**
When we invoke commands through spark-shell the driver converts the spark application into various spark jobs which in turn converts each job into multiple DAG's .
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image007.png)

**Spark Stages**
As park of DAG nodes stages re created based on the operations which needs to be performed .
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image008.png)

**Spark Tasks**
Each stage is comprised of multiple tasks which is a unit of execution which gets federated across the spark executors . Each task maps to a single core and works on a single partition of data .

For an executor with 16 core 16 or more tasks would run working on 16 or more partitions in parallel .
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image009.png)

## **Transformations Actions and Lazy Executions**
Transformations transform a spark dataframe into a new dataframe without altering the original data as it is immutable . E.g. a select() or a filter() command will not change the original dataframe but will return a new dataframe

All transformations are evaluated lazily aka the results are not computed immediately but are recorded which allows spark to rearrange the transformations , optimize them into stages for efficient execution . Lazy evaluation allows spark to record transformations until an action is invoked . Each transformation produces a new dataframe .
Actions : Anything which triggers execution of a transformation like show, count etc.
(file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image010.png)

## **Narrow and Wide Transformations**

**Narrow Transformation** : Where a single output is partition can be computed from a single input partition . E.g. filter(), contains() operate on single partition .

**Wide Transformations** : Where a shuffle of partitions happens . E.g. groupby(), orderby() leads data to be read from many partitions combine them and then written to disk .

![](file:///C:/Users/Z58/AppData/Local/Temp/msohtmlclip1/02/clip_image011.png)
