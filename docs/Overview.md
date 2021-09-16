
# **Introduction**

## **Why Spark?**

Hadoop was the first version of distributed computing

Issues with Hadoop

  - Not Unified You need to use Hive to write SQL , Mahout for ML , Zookeeper as cluster manager etc
  - Hard to manage and administer
  - Batch processing using Map Reduce
  - Large Datasets were written to disk for subsequent stages of operation which meant high i/o.
  - OK for batch processing but was slow while doing Machine learning / Streaming
  - No support for multiple languages apart from Java

Spark was created in order to overcome these obstacles .

## **What is Spark?**

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

## **Lets Revisit hadoop issues and compare those with Spark :**

- Not Unified You need to use Hive to write SQL , Mahout for ML , Zookeeper as cluster manager etc -> Spark is unified with all ML, Streaming , SQL functionalities built in the core itself .
- Hard to manage and administer -> Spark is easy to Manage and administer
- Batch processing using Map Reduce -> Spark is must faster than traditional map reduce
- Large Datasets were written to disk for subsequent stages of operation which meant high i/o.-> Spark writes the data in memory making it much faster
- OK for batch processing but was slow while doing Machine learning / Streaming-> Spark is used for real time streaming and data processing .
- No support for multiple languages apart from Java -> Spark supports Python , Java , R, Scala natively

## **Apache Spark Components as a Unified Stack**

![](/docs/Overview/overview.png)


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

![](/docs/Overview/2.png)

**Spark Driver :**

-   Instantiates a spark Session
-   Maintains information about the spark application.
-   It’s the heart of a Spark Application and maintains all relevant information during the lifetime of   the application.
-   It Communicates with cluster manager , requests resources from Cluster Manager for spark execution (JVM) , transforms spark operations into DAG, schedules operations and co-ordinates with executors .

**Spark Session**

-   The Spark application is controlled using SparkSession.
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
![](/docs/Overview/3.png)

**Spark Executor**

-   Runs on each worker node in the cluster
-   Communicates with driver program and execute tasks on the worker nodes
-   They have 2 major responsibilities
  - Execute Code assigned to it by the driver
  - Reporting state of computation on that executor back to the driver node .

## **Distributed Data and Partitions**

-   Actual Physical data is stored as partitions. It is a collection of rows that sit on one physical machine in your cluster.
-   Spark treats each partition as a dataframe in memory
-   Spark Executor reads the data from its closest partition
-   Partitioning allows parallelism as Each core in partition is assigned to its own data partition to work with .
-   If you have one partition, Spark will have a parallelism of only one, even if you have thousands of executors. If you have many partitions but only one executor, Spark will still have a parallelism of only one because there is only one computation resource.

![](/docs/Overview/4.png)

Partitioning allows efficient parallelism . In distributed environment spark executor reads data from the nearest partition allowing efficient parallelism .
![](/docs/Overview/5.png)

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
![](/docs/Overview/6.png)

**Spark Jobs**
When we invoke commands through spark-shell the driver converts the spark application into various spark jobs which in turn converts each job into multiple DAG's .
![](/docs/Overview/7.png)

**Spark Stages**
As park of DAG nodes stages re created based on the operations which needs to be performed .
![](/docs/Overview/8.png)

**Spark Tasks**
Each stage is comprised of multiple tasks which is a unit of execution which gets federated across the spark executors . Each task maps to a single core and works on a single partition of data .

For an executor with 16 core 16 or more tasks would run working on 16 or more partitions in parallel .
![](/docs/Overview/9.png)

## **Transformations Actions and Lazy Executions**
Transformations transform a spark dataframe into a new dataframe without altering the original data as it is immutable . E.g. a select() or a filter() command will not change the original dataframe but will return a new dataframe.

Transformations are the core of how you express your business logic using Spark.

All transformations are evaluated lazily aka the results are not computed immediately but are recorded which allows spark to rearrange the transformations , optimize them into stages for efficient execution . Lazy evaluation allows spark to record transformations until an action is invoked . Each transformation produces a new dataframe .



Actions : Anything which triggers execution of a transformation like show, count etc.

Kind of actions
- Actions to view data in the console
- Actions to collect data to native objects in the respective language
- Actions to write to output data sources
-
![](/docs/Overview/10.png)

## **Narrow and Wide Transformations**

**Narrow Transformation** : Where a single output is partition can be computed from a single input partition . E.g. filter(), contains() operate on single partition .

**Wide Transformations** : Where a shuffle of partitions happens . E.g. groupby(), orderby() leads data to be read from many partitions combine them and then written to disk .

![](/docs/Overview/11.png)

## **An End-to-End Example**

We’ll use Spark to analyze some [flight data](https://github.com/databricks/Spark-The-Definitive-Guide/tree/master/data/flight-data) from the United States Bureau of Transportation statistics.

Below is how data looks in csv file

![](/docs/Overview/12.png)
```Python
$ head /data/flight-data/csv/2015-summary.csv
```
```Python
DEST_COUNTRY_NAME,ORIGIN_COUNTRY_NAME,count
United States,Romania,15
United States,Croatia,1
United States,Ireland,344
```

We will use below code to read the csv into a spark Dataframe

```Python
flightData2015 = spark\
  .read\
  .option("inferSchema", "true")\
  .option("header", "true")\
  .csv("/data/flight-data/csv/2015-summary.csv")
```
![](/docs/Overview/13.png)

The Dataframe just created has a set of columns with an unspecified number of rows. The reason the number of rows is unspecified is because reading data is a transformation, and is therefore a lazy operation.

Spark peeked at only a couple of rows of data to try to guess what types each column should be.

Below provides an illustration of the CSV file being read into a Dataframe and then being converted into a local array or list of rows.

![](/docs/Overview/14.png)


If we perform the take action on the Dataframe, we will be able to see the same results that we saw before when we used the command line:
flightData2015.take(3)

![](/docs/Overview/15.png)


Array([United States,Romania,15], [United States,Croatia...



Let’s specify some more transformations! Now, let’s sort our data according to the count column, which is an integer type. Below figure illustrates this process. Also Sort does not modify the DataFrame. We use sort as a transformation that returns a new DataFrame by transforming the previous DataFrame.

![](/docs/Overview/16.png)

Nothing happens to the data when we call sort because it’s just a transformation. However, we can see that Spark is building up a plan for how it will execute this across the cluster by looking at the explain plan. We can call explain on any DataFrame object to see the DataFrame’s lineage (or how Spark will execute this query):

flightData2015.sort("count").explain()


== Physical Plan ==
*Sort [count#195 ASC NULLS FIRST], true, 0
+- Exchange rangepartitioning(count#195 ASC NULLS FIRST, 200)
   +- *FileScan csv [DEST_COUNTRY_NAME#193,ORIGIN_COUNTRY_NAME#194,count#195] ...

You can read explain plans from top to bottom, the top being the end result, and the bottom being the source(s) of data. In this case, take a look at the first keywords. You will see sort, exchange, and FileScan. That’s because the sort of our data is actually a wide transformation because rows will need to be compared with one another.

Now, just like we did before, we can specify an action to kick off this plan. However, before doing that, we’re going to set a configuration. By default, when we perform a shuffle, Spark outputs 200 shuffle partitions. Let’s set this value to 5 to reduce the number of the output partitions from the shuffle:

spark.conf.set("spark.sql.shuffle.partitions", "5")
flightData2015.sort("count").take(2)
... Array([United States,Singapore,1], [Moldova,United States,1])
Below figure illustrates this operation. Notice that in addition to the logical transformations, we include the physical partition count, as well.


The logical plan of transformations that we build up defines a lineage for the DataFrame so that at any given point in time, Spark knows how to recompute any partition by performing all of the operations it had before on the same input data. This sits at the heart of Spark’s programming model—functional programming where the same inputs always result in the same outputs when the transformations on that data stay constant.
We do not manipulate the physical data; instead, we configure physical execution characteristics through things like the shuffle partitions parameter . We ended up with five output partitions because that’s the value we specified in the shuffle partition. You can change this to help control the physical execution characteristics of your Spark jobs. In experimenting with different values, you should see drastically different runtimes. You can monitor the job progress by navigating to the Spark UI on port 4040 to see the physical and logical execution characteristics of your jobs.
