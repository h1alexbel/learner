# Data processing on AWS

## AWS Glue

Serverless service that handles discovery and definition
of table definitions and schema:
1. S3 data lakes
2. RDS
3. Redshift
4. other RDBMS

Also, custom ETL jobs:
1. Trigger-driven, on a schedule, or on demand
2. Fully managed

**ETL jobs uses Apache Spark under the hood**.

#### Glue Crawler

Glue Crawler scans data in S3, and **creates schema**.
Populates Glue Data Catalog: **stores only table definition,
original data stays in S3**.

**Once cataloged, you can treat your unstructured data in S3
like it's structured**.

Glue crawler will extract partitions based on how your S3 data is organized.
Partition your data based on how you will be querying your data lake.

#### Glue with Hive

Hive lets you run SQL-like queries from EMR.
Glue can be integrated with Hive:
1. Glue Data Catalog can serve as a Hive 'metastore'.
2. Hive metastore can be imported into Glue.

#### Glue ETL

Automatic code generation based on a graphical data pipeline,
in Scala or Python, that will be run
on Spark.
Target can be S3, JDBC (RDS, Redshift), or on Glue Data Catalog.

**All data that comes into Glue ETL is called DynamicFrame(
similar to Spark's DataFrame)**.

Glue Transformations are:
* `DropFields`, `DropNullFields`: remove (null) fields.
* `Filter`: specify a function to filter records
* `Join`: to enrich data
* `Map`: add fields, delete fields, perform external lookups
Machine Learning Transformations:
* `FindMatches ML`: identity duplicate or matching records in a dataset, even when the
  records do not have a common unique identifier and no fields match exactly
Format conversions:
* CSV
* JSON
* Avro
* ORC
* XML
And all Apache Spark transformations.
<br>
`ResolveChoice` deals with ambiguities in a DynamicFrame and returns a new one:
for instance, two fields with the same name can be handled by:
1. `make_cols`: creates a new column for each type
2. `cast`: casts all values to a specified type
3. `make_struct`: creates a structure that contains each data type
4. `project`: projects every type to a given type

**Also, Glue supports Streaming running on Apache Spark Structured Streaming**.

#### Job bookmarks 

There are several ways to run Glue jobs:
1. Cron schedule
2. Job bookmarks: persists state from job run,
   preventing old data reprocessing;
   handles only new rows, not updated ones
3. CloudWatch Events

#### Glue Studio

**Visual interface for ETL workflows, the visual job editor**.
That allows you to create DAG's for workflows.
**You can specify the mappings, transformations, and so on**.
Also, includes visual job dashboard: overview, status and run times.

#### Glue Data Quality

Data quality can be controlled and monitored automatically
integrating it into Glue jobs.
Glue operates with Data Quality Definition Language (DQDL).
Results can be used to fail the job, or just be reported
to CloudWatch.

![quality.png](assets/quality.png)

#### Glue DataBrew

Glue DataBrew is a visual data preparation tool.
You create 'recipes' of transformations that can be saved as jobs
within a larger project.
May define data quality rules.

#### Cost model

**Glue bills by the second for crawler and ETL jobs**.
The first million objects stored and accesses are free for the Glue
Data Catalog.
Development endpoints for developing ETL code charged by the minute.

#### Glue Anti-patterns

* Multiple ETL engines: 
  **Glue is based on Spark**, if you want to use other engines, check out
  Data Pipeline or EMR.

## AWS Lake Formation

Lake Formation is built on top of [Glue](#aws-glue).
Makes it easy to set up a secure data lake.
Everything in Glue you can do with Lake Formation.

![lake.png](assets/lake.png)

* Access control
* Auditing

## AWS Security Lake

Separate version of [lake formation](#aws-lake-formation),
built for security teams.
**Centralizes security data**.
Normalizes data across AWS and on-premises.

## AWS EMR

**Elastic MapReduce (EMR): managed Hadoop cluster** on EC2 instances.
Includes Spark, HBase, Presto, Flink, Hive and more.
Has its own EMR Notebooks.
Frameworks and applications are specified at cluster launch.

EMR Cluster basically is just a collection of EC2 instances:
1. **Master node**: manages cluster
2. **Core node**: host HDFS data and runs tasks
3. **Task node** (Optional): run tasks, does not host data, good use of **spot instances**

### Transient cluster vs. Long-running:
transient cluster will terminate once all steps are complete, saves money;
long-running cluster must be manually terminated.

### Storage

1. HDFS: Hadoop Distributed File System,
   multiple copies stored across nodes for redundancy,
   files stored as blocks (128 MB default size),
   **data is ephemeral: HDFS data is lost when the cluster is terminated**;
   useful for caching intermediate results or workloads with random I/O.
2. EMRFS: access S3 as if it were HDFS,
   **allows persistent storage after cluster termination**,
   strongly consistent, uses DynamoDB to track consistency.
3. Local file system: suitable only for temporary data (buffers, caches).
4. EBS for HDFS: allows use of EMR on EBS-only types (M4, C4),
   deletes volumes when the cluster is terminated;
   **EBS volumes can only be attached when launching a cluster**;
   if you manually detach an EBS volume, EMR treats that as a failure
   and replaces it.

### Policy

**EMR charges by the hour, plus EC2 charges**.
Provision new nodes if core node fails.
* EMR can add and remove tasks nodes on the fly:
  increase processing capacity, but not HDFS capacity.
* EMR can resize a running cluster's core nodes:
  increases both processing and HDFS capacity.

**Adding and removing task nodes on the fly is a good way of dealing with
temporary surges**.

### Managed Scaling

Introduced in 2020, support instance groups and instance fleets.
Scales sport, on-demand, and instances in a Savings Plan within the same cluster.
Available for Spark, Hive, and YARN workloads.

### Hadoop

Hadoop core consists of:
1. **MapReduce**: maps data to key/value pais,
   reduces intermediate results to final output.
2. **YARN**: yet another resource negotiator,
   manages cluster resources for multiple data processing frameworks.
3. **HDFS**: hadoop distributed file system, distributes data blocks
   across cluster in a redundant manner, ephemeral in EMR, data lost on termination.

### EMR Serverless

Instead of login to the master node,
you can use EMR Serverless:
choose an EMR Release and Runtime (Spark, Hive, Presto)
and then submit queries/scripts via job run requests.
EMR manages underlying capacity, but you can specify pre-initialized capacity.
**Remember that Spark adds 10% overhead to this capacity for executors and drivers**.
**EMR Serverless solves a problem with capacity planning and provisioning**.


EMR Serverless Application lifecycle:

![emr-lifecycle.png](assets/emr-lifecycle.png)

This isn't automatic: you must initiate API call, such as
`CreateApplication`, `StartApplication`, `StopApplication` and `DeleteApplication`.

### EMR on EKS

EMR on EKS allows submitting a Spark job on EKS without provisioning
EMR clusters.

![emr-modes.png](assets/emr-modes.png)

### Instance Types

**Master node**:
* `m5.xlarge` if < 50 nodes, larger if > 50 nodes
**Core and Task nodes**:
* `m5.xlarge`, `m4.xlarge`, depends on the workload (CPU/Memory/Network),
  **Spot Instances** is a good choice for Task nodes. 

### Spark

Distributed processing framework for big data.
**In-memory caching, optimized query execution**.
Supports Java, Scala, Python, and R.

Supports code reuse across:
* Batch processing
* Interactive queries
* Real-time analytics
* Machine Learning
* Graph processing
Support of stream processing using
Spark Streaming, can be integrated with Kinesis, Kafka.

Some code executed in clustered network, **while some code executed by driver**.

**Spark is not a tool for OLTP**.

Spark apps are run as an independent processes
on a cluster.
The `SparkContext` (driver program) coordinates them through a
`Cluster Manager` (Spark YARN).
Cluster Manager distributes tasks across `Executor`'s,
which run them and store data.
**SparkContext sends application coed and related tasks to executors**.

![spark.png](assets/spark.png)

Spark components:
* **Spark Core**: memory management, fault recovery, scheduling, job management,
  interact with storage.
* **Spark Streaming**: Real-time streaming analytics, structured streaming;
  integration with Kafka, Flume, HDFS, ZeroMQ.
* **Spark SQL**: up to 100x faster than MapReduce,
  SQL-style interface to underlying data on EMR cluster;
  JDBC, ODBC, JSON, HDFS, ORC, HiveQL
* **MLLib**
* **GraphX**

### RDD

#### Transformations

#### Actions



### Structured Streaming

Spark Streaming operates with a term of **DataSet** or DataFrame in Python.
DataSet is just basically a giant table of data, so you can treat your data as a large
database table.

Spark Streaming can be integrated with Kinesis, Redshift, Athena
using the related packages.

### Hive on EMR

Hive uses SQL-like syntax, called HiveQL,
which describes mappings for unstructured data 
to make it 'structured'.

**Hive is a tool for OLAP queries, and is not designed to work
with OLTP/stream processing**.

Hive maintains a **metastore** that imparts a structure to you
define on the unstructured data that is stored on HDFS.


By default, Hive maintains metastore in MySQL database on the master node
of your cluster.
External metastore can boost availability.
Such tools like: **AWS RDS**, **AWS Glue Data Catalog** can be used 
as external metastore.
Also, you can write tables in **S3**, load scripts from S3,
and use DynamoDB as an external table.

### Apache Pig

Instead of MapReduce, instead of creating mappers and reducers
Pig introduces **Pig Latin**, a scripting language with SQL-like syntax
to define a map and reduce steps.

Highly extensible with User Defined functions.

### HBase

HBase is a non-relational, petabyte-scale database.
**Based on Google BigTable, on top of HDFS**.
Operates in-memory, integrates with [Hive](#hive-on-emr).

DynamoDB vs. HBase:
`DynamoDB`:
* Fully managed, serverless
* More integration with AWS
* Glue integration

`HBase`:
* Efficient storage of sparse data
* Appropriate for high-frequency counters
* High write throughput
* More integration with Hadoop

HBase can be integrated not only with HDFS,
EMRFS is also supported.

### Presto

Presto can connect to **many different databases and data stores
at once**, **and query across them**.
Familiar SQL-like syntax, optimized for OLAP.

### Hue

**Hadoop User Experience** is a Hadoop UI for applications on your EMR cluster.
Can browse and move data between HDFS and S3.