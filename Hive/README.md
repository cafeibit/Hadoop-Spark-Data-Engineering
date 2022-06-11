# Hive Basic <a href="https://docs.microsoft.com/en-us/azure/hdinsight/hadoop/hdinsight-use-hive">What is Apache Hive and HiveQL on Azure HDInsight?</a>

## <a href="https://lakefs.io/hive-metastore-why-its-still-here-and-what-can-replace-it/">Hive Metastore – Why It’s Still Here and What Can Replace It? by Einat Orr, PhD</a>

**Hive & Hadoop — A Brief History**

Apache Hive burst onto the scene in 2010 as a component of the Hadoop ecosystem, when Hadoop was the novel and innovative way of doing big data analytics. 
What Hive did was implement a SQL interface to Hadoop. Its architecture consisted of two main services:

* A Query Engine — responsible for SQL statement execution.
* A Metastore — responsible for virtualization of data collections in HDFS as tables.

**The concept behind Hadoop was revolutionary**. Huge datasets are stored in a distributed filesystem (**HDFS**) running on clusters of commodity hardware. Compute jobs execute in parallel alongside the data utilizing MapReduce. The distribution of these tasks is managed by Yarn. The main interface is a programming language, initially Java or Scala.

All of the components became open sourced under the Apache foundation and were free to use. For several years, this collection of technologies stood as the standard for large-scale analytics. 

Piece by piece, however, the stack was dismantled by newer technologies…

HDFS gave way to object storages, led by AWS S3. MapReduce has been dethroned by Spark, which over time also reduced its dependency on Hadoop. Yarn is being replaced by technology like Kubernetes. And the query engine component of Hive has been surpassed in performance and adoption by Presto/Trino.

Despite this evolution, most organizations featuring data lakes still have an active Hive Metastore deployment as part of their architecture. Compared to its Hadoop counterparts you might be wondering, “What makes Hive Metastore so special?”

To answer this question, let’s dive a bit deeper into what functions Hive Metastore provides today, and what technologies have emerged to replace it.

### What Hive Metastore Does

When new data is saved to object storage, we register it into Hive Metastore by calling the metastore API from the code of any data application or orchestration tool. This declarative phase maps a set of objects in the object store to a table exposed by Hive. Part of registration includes specifying the schema of the table held in the file, with some metadata describing the columns.

Using Hive Metastore in this way provides four main benefits related to:

* Virtualization
* Discoverability
* Schema Evolution
* Performance

Let’s discuss these in more detail!

**Virtualization**
Data analysts using SQL usually aren’t interested in the details of object storage and its access patterns. They’d simply like to have their tables, please!

This dynamic is the driving force that makes Hive Metastore irreplaceable while other Hadoop components were replaced. Every new technology that was introduced made sure to support Hive Metastore to avoid breaking critical analytic workflows dependent upon the table objects defined in Hive.

**Discoverability**
Hive Metastore naturally becomes a catalog of all the collections held in object storage when exposing new data is accompanied by updating it. If well maintained, this allows for the discovery of data sets available to query.

Additionally, supplemental can be saved in the metastore to provide helpful information about the data like its update frequency, who owns it, etc.

**Schema Evolution**
One of the challenges of managing data sets over time is their mutability. Records may change over time with respect to the existing columns describing their attributes. Or the set of attributes itself changes over time, resulting in a change to the schema of the table. 

The registration process described above provides a record of the schema for each additional data file that belongs to the table. This means that if the schema has changed at some point in time, it will be recorded within the Hive Metastore. When accessing the data, it can be accessed with the appropriate schema. 

This also provides a good basis to validate a schema if it should not have changed, and alert on it. Hive holds the information to create such a test.

**Performance**
Since Hive Metastore maps the table to the underlying object, it allows the representation of partitions according to the primary key supported by the object storage. The granularity of the partitions can be set by the user, and if partitions are balanced and their number is reasonable, this mapping allows improvement in query performance.

This is often referred to as “partition pruning”, which allows a query engine to identify data files that can be skipped.

**Will Hive Survive the Next Revolution?**

There are no candidates to directly replace the metastore, but it may become obsolete if some existing trends take hold and play well together.
Let’s take a look at the leading successors.

**Open Table Formats**
Iceberg, Hudi, and Delta Lake are the three players in this category. Each one was created to answer a different need, but over time they all converge to cover the set of features that allow: 

* Mutability (Hudi, Delta)
* Efficiency in accessing large tables (Iceberg)
* Schema enforcement and evolution (Delta).

When using applications that support these formats, the data can be treated as a table by the application without any intermediates. Not all applications in the ecosystem support the formats, and there are performance implications to using them for some use cases.

Since Hive Metastore is a general interface supported by all applications, organizations using an open table format still rely on Hive for virtualization, and/or for other use cases not covered by the formats.

**Data Catalogs**
In the past year-plus, we’ve witnessed a blitz of over 10 open source discovery tools released by leaders in the data engineering space, indicating the need for an organization level data catalog. These newcomers join other existing commercial data catalogs offering, such as Allation.

Catalogs support a mapping of an object store together with most databases in use today. Many discovery tools leverage the data already in Hive Metastore if possible, otherwise going into the object store if not. Not surprisingly, these tools are good candidates to replace the cataloging functionality of Hive Metastore over time.

**Observability Tools**
The main goal of observability tools is to monitor the quality of the data pipelines operationally and the data itself. Some tools focus on the former (e.g. Databand), while others focus on the latter (Great Expectations or Monte Carlo). If the observability tool is implemented throughout the data lifecycle, it can dynamically update a data catalog and replace Hive Metastore as a catalog.

**Final Thoughts**
A number of technologies have started to chip away at improving upon Hive’s functionality. But no single one is mature enough yet, and no consensus has been reached on a combination to successfully remove Hive Metastore from the picture.

This does not mean it should or will remain a part of data architectures. In fact, it suffers from apparent deficiencies in both usability and performance. Notably Hive Metastore:

* Is difficult to install and maintain.
* Not architected cloud-native, complicating managed service implementations.
* Suffers scalability restrictions from relational DB reliance.

All of these factors combine to make us predict Hive Metastore won’t survive the next evolution of data architectures. This won’t happen automatically — it requires momentum to build from within the community. We hope you collaborate with us to make a better future!


## How to tune Hive performance

Why is it said that performance optimization is difficult, because the optimization of a technology must be a comprehensive work, which is a combination of multiple technologies. If we are limited to only one technology, then we will definitely not be able to optimize. The following will introduce the diversity of Hive optimization from several completely different perspectives. Let’s experience it together first.

### 1. SQL statement optimization
There are too many contents involved in SQL statement optimization, which cannot be introduced one by one due to limited space, so I will take a few typical examples to let everyone learn this kind of thinking. In the future, if you encounter similar tuning problems, you can go to these aspects. Think about it.

#### 1. union all
```
insert into table stu partition(tp)
select s_age,max(s_birth) stat,'max' tp
from stu_ori
group by s_age

union all

insert into table stu partition(tp)
select s_age,min(s_birth) stat,'min' tp
from stu_ori
group by s_age;
```
We simply analyze the above SQl statement, that is, get the maximum and minimum birthdays of each age and put them in the same table. The two statements before and after union all group the same table according to s_age, and then take the maximum and minimum value. Grouping the same field in the same table twice is a huge waste. Can we transform it? Of course, we can. I will introduce a syntax for you: `from ... insert into ...` , this syntax will be from prepend, the function is to use a table, you can perform multiple insert operations:
```
--Enable dynamic partitioning
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;

from stu_ori

insert into table stu partition(tp)
select s_age,max(s_birth) stat,'max' tp
group by s_age

insert into table stu partition(tp)
select s_age,min(s_birth) stat,'min' tp
group by s_age;
```
The above SQL can group the s_age field of the stu_ori table once and perform two different insert operations.

This example tells us that we must know more about SQL statements. If we don't know this syntax, we will never think of this way.

#### 2. distinct

Let's look at a SQL first, to remove the double count:
```
select count(1)
from(
   select s_age
   from stu
   group by s_age
) b;
```
This is a simple count of the enumeration values of age, why not use distinct?
```
select count(distinct s_age)
from stu;
```
Some people say that using the first method can effectively avoid the data skew on the Reduce side when the amount of data is particularly large, but is this true?

Regardless of the large amount of data, using distinct in the current business and environment will definitely be more efficient than the above sub-query method. The reasons are as follows:

* The deduplicated field above is the age field. You must know that the enumeration value of age is very limited. Even if you calculate the age between 1 and 100 years old, the maximum enumeration value of s_age is 100. If it is converted into MapReduce to explain If so, in the Map stage, each Map will deduplicate s_age. Since the s_age enumeration value is limited, the s_age obtained by each map is also limited, and the amount of reduced data is finally the number of maps * the number of s_age enumeration values.

* The distinct command will build a hashtable in memory, and the time complexity of searching and deduplication is O(1). By sorting, the optimal time complexity of sorting cannot reach O(1). In addition, the first method (group by) deduplication will be converted into two tasks, which will consume more disk network I/O resources.

* The latest Hive 3.0 adds count(distinct ) optimization. By configuring hive.optimize.countdistinct, it can be automatically optimized even if the data is skewed, and the logic of SQL execution can be changed automatically.

* The second method (distinct) is more concise than the first method (group by), and the meaning of the expression is simple and clear. If there are no special problems, the concise code is excellent!

This example tells us that sometimes we should not over-optimize. Tuning pays attention to timely tuning. Premature tuning may result in useless work or even negative effects. The cost of work in tuning is not proportional to the return. Tuning needs to follow certain principles.

### 2. Data format optimization

Hive provides a variety of data storage organization formats, and different formats will have a great impact on the operating efficiency of the program.

The formats provided by Hive are TEXT, SequenceFile, RCFile, ORC and Parquet, etc.

SequenceFile is a flat file with a binary key/value pair structure and was widely used as a MapReduce output/output format on early Hadoop platforms, as well as as a data storage format.

Parquet is a columnar data storage format that is compatible with multiple computing engines, such as MapRecue and Spark. It provides good performance support for multi-layer nested data structures and is currently one of the mainstream choices for data storage in Hive production environments.

ORC optimization is an optimization of RCFile. It provides an efficient way to store Hive data, and can also improve Hive's performance of reading, writing, and processing data, and is compatible with multiple computing engines. In fact, in the actual production environment, ORC has become one of the mainstream choices of Hive for data storage.

We use the same data and SQL statements, but the data storage format is different, and we get the following execution time:
```
Data format   CPU time  User waiting time
TextFile	    33m	      171s
SequenceFile	38m       162s
Parquet	      2m22s	    50s
ORC	          1m52s	    56s
```
**Note:**
  CPU time: Indicates the time of server CPU resources occupied by running programs.
  User waiting time: It records all the time the user waits from submitting the job to returning the result.

It takes 33 minutes to query the CPU of TextFile type data table, and 1 minute and 52 seconds to query the ORC type table. The time is greatly shortened. It can be seen that different data storage formats can also have a great impact on HiveSQL performance.

### 3. Too much optimization for small files
If there are too many small files, for hive, when querying, each small file will be regarded as a block, and a Map task will be started to complete, and the time of starting and initializing a Map task is much longer than the time of logical processing. It will cause a huge waste of resources. Also, the number of Maps that can be executed at the same time is limited.

Therefore, it is necessary for us to optimize too many small files. Regarding the solution to too many small files, I wrote an article to explain the solution. For details, please refer to:

**Cause of small files**

The small files in hive must be generated when importing data into the hive table, so let’s take a look at several ways to import data into hive

* Insert data directly into the table
```
insert into table A values ​​(1,'zhangsan',88),(2,'lisi',61);
```
This method will generate a file each time it is inserted, and multiple small files will appear when a small amount of data is inserted many times. However, this method is rarely used in the production environment, and it can be said that it is basically not used.

* Load data by load method
```
load data local inpath '/export/score.csv' overwrite into table A -- import file

load data local inpath '/export/score' overwrite into table A -- import folder
```
Use the load method to import files or folders. When importing a file, there is one file in the hive table. When importing a folder, the number of files in the hive table is the number of all files in the folder.

* Load data by query
```
insert overwrite table A select s_id,c_name,s_score from B;
```
This method is commonly used in production environments, and it is also the easiest way to generate small files. When insert imports data, the MR task will be started, and as many files as there are reducers in MR, the number of files will be output. So, `the number of files = the number of ReduceTask * the number of partitions`

There are also many simple tasks without reduce, only the map stage, then `Number of files = Number of MapTasks * Number of partitions`

Every time insert is executed, at least one file is generated in hive, because there will be at least one MapTask when insert is imported. For example, some businesses need to synchronize data to hive every 10 minutes, which will generate a lot of files.

**Effects of too many small files**

1. First of all, for the underlying storage HDFS, HDFS itself is not suitable for storing a large number of small files. Too many small files will cause the namenode metadata to be very large, occupy too much memory, and seriously affect the performance of HDFS.

2. For hive, when querying, each small file will be regarded as a block, and a Map task will be started to complete, and the time of starting and initializing a Map task is much longer than the time of logical processing, which will cause a lot of resources. waste. Also, the number of Maps that can be executed at the same time is limited.
//////////////////////
**How to fix too many small files**

#### 1. Use the concatenate command that comes with hive to automatically merge small files
Instructions:
```
#For non-partitioned tables
alter table A concatenate;

# For partitioned table
alter table B partition(day=20201224) concatenate;
```
Example:
```
#Insert data into table A
hive (default)> insert into table A values ​​(1,'aa',67),(2,'bb',87);
hive (default)> insert into table A values ​​(3,'cc',67),(4,'dd',87);
hive (default)> insert into table A values ​​(5,'ee',67),(6,'ff',87);

#Execute the above three statements, then there will be three small files under the A table, execute the following statement on the hive command line
#View the number of files under table A
hive (default)> dfs -ls /user/hive/warehouse/A;
Found 3 items
-rwxr-xr-x 3 root supergroup 378 2020-12-24 14:46 /user/hive/warehouse/A/000000_0
-rwxr-xr-x 3 root supergroup 378 2020-12-24 14:47 /user/hive/warehouse/A/000000_0_copy_1
-rwxr-xr-x 3 root supergroup 378 2020-12-24 14:48 /user/hive/warehouse/A/000000_0_copy_2

#You can see that there are three small files, and then use concatenate to merge
hive (default)> alter table A concatenate;

#Check the number of files under table A again
hive (default)> dfs -ls /user/hive/warehouse/A;
Found 1 items
-rwxr-xr-x 3 root supergroup 778 2020-12-24 14:59 /user/hive/warehouse/A/000000_0

# has been merged into one file
```
Notice: 
1. The concatenate command only supports RCFILE and ORC file types.
2. When using the concatenate command to merge small files, the number of merged files cannot be specified, but the command can be executed multiple times.
3. The number of files does not change after using concatenate for many times. This is related to the setting of the parameter mapreduce.input.fileinputformat.split.minsize=256mb, and the minimum size of each file can be set.

#### 2. Adjust parameters to reduce the number of Maps

**Set the relevant parameters of map input merging small files:**

```
#Merge small files before executing Map
#CombineHiveInputFormat The bottom layer is Hadoop's CombineFileInputFormat method
#This method is to combine multiple files into a split as input in mapper
set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat; -- default

#Maximum input size of each Map (this value determines the number of merged files)
set mapred.max.split.size=256000000; -- 256M

#The minimum size of split on a node (this value determines whether files on multiple DataNodes need to be merged)
set mapred.min.split.size.per.node=100000000; -- 100M

#The minimum size of split under a switch (this value determines whether files on multiple switches need to be merged)
set mapred.min.split.size.per.rack=100000000; -- 100M
```

**Set the relevant parameters for merging map output and reduce output:**

```
#Set the map output to be merged, the default is true
set hive.merge.mapfiles = true;

#Set the reduce side output to be merged, the default is false
set hive.merge.mapredfiles = true;

#Set the size of the merged file
set hive.merge.size.per.task = 256*1000*1000; -- 256M

#When the average size of the output file is less than this value, start a separate MapReduce task for file merge
set hive.merge.smallfiles.avgsize=16000000; -- 16M
```

**enable compression**

```
# Whether to compress the query result output of hive
set hive.exec.compress.output=true;

# Whether to use compression for the result output of MapReduce Job
set mapreduce.output.fileoutputformat.compress=true;
```

#### 3. Reduce the number of Reduces

The number of #reduce determines the number of output files, so you can adjust the number of reduce to control the number of files in the hive table,
The partition function distribute by in #hive happens to control the partition partition in MR.
```
#Then by setting the number of reduce, combined with the partition function, the data can be entered into each reduce in a balanced manner.

#There are two ways to set the number of reduce, the first is to directly set the number of reduce
set mapreduce.job.reduces=10;

#The second is to set the size of each reduce, Hive will guess the number of reduce based on the total size of the data
set hive.exec.reducers.bytes.per.reducer=5120000000; -- default is 1G, set to 5G

#Execute the following statement to evenly distribute the data to reduce
set mapreduce.job.reduces=10;
insert overwrite table A partition(dt)
select * from B
distribute by rand();
```
Explanation: If you set the reduce number to 10, use rand() to randomly generate a number x % 10 ,
In this way, the data will be randomly entered into reduce, preventing some files from being too large or too small.

#### 4. Use hadoop's archive to archive small files
Hadoop Archive, referred to as HAR, is a file archiving tool that efficiently puts small files into HDFS blocks. It can package multiple small files into a HAR file, which reduces the memory usage of the namenode while still allowing the file to be transparent. Access
```
#Used to control whether the archive is available
set hive.archive.enabled=true;
#Notify whether Hive can set the parent directory when creating an archive
set hive.archive.har.parentdir.settable=true;
#Control the size of the file to be archived
set har.partfile.size=1099511627776;

#Use the following command to archive
ALTER TABLE A ARCHIVE PARTITION(dt='2020-12-24', hr='12');

#Restore the archived partition to the original file
ALTER TABLE A UNARCHIVE PARTITION(dt='2020-12-24', hr='12');
```
**Notice:**  
  The archived partition can be viewed and cannot be inserted overwrite, and must be unarchived first.

If it is a new cluster and there are no historical problems, it is recommended that hive use the orc file format and enable lzo compression.
If there are too many small files, you can use hive's own command concatenate to quickly merge.
////////////////////////////

### 4. Parallel execution of optimizations
Hive turns a query into one or more stages. Such stages can be MapReduce stage, sampling stage, merge stage, limit stage. Or other stages that may be required during Hive execution. By default, Hive will only execute one stage at a time. However, a particular job may contain many stages, and these stages may not be completely dependent on each other, which means that some stages can be executed in parallel, which may shorten the execution time of the entire job. If there are more stages that can be executed in parallel, the faster the job may complete.

By setting the parameter hive.exec.parallel to true, concurrent execution can be enabled. In a shared cluster, it should be noted that if there are more parallel stages in the job, then the cluster utilization will increase.
```
set hive.exec.parallel=true; //Open tasks for parallel execution
set hive.exec.parallel.thread.number=16; //The same sql allows the maximum parallelism, the default is 8.
```
Of course, there is an advantage when the system resources are relatively idle, otherwise, there are no resources, and parallelism will not be possible.

### 5. Data skew optimization

The principle of data skew is known, that is, one or several keys occupy 90% of the entire data, so the efficiency of the entire task will be slowed down by the processing of this key, and at the same time, the same keys may be aggregated together to cause memory loss overflow.

The general processing scheme of Hive's data skew:

Common practice, through parameter tuning:

* set hive.map.aggr=true;
* set hive.groupby.skewindata = true;

When the option is set to true, the generated query plan has two MapReduce tasks.

In the first MapReduce, the output result set of the map will be randomly distributed among the reducers, and each reducer will perform partial aggregation operations and output the results.

The result of this processing is that the same Group By Key may be distributed to different reducers, so as to achieve the purpose of load balancing;

The second MapReduce task is then distributed to the reduce by Group By Key according to the preprocessed data results (this process can ensure that the same Group By Key is distributed to the same reduce), and finally the final aggregation operation is completed.

#### So how to deal with this kind of data skew in the case of daily needs:

1. Sample sampling, which sets of keys are obtained;

2. Add random numbers to the keys in the set according to certain rules;

3. When joining is performed, data skew is avoided because it is broken up;

4. In the processing result, the previously added random numbers are divided into original data.

For example: if it is found that 90% of the keys are null, once the data volume is too large, the data will inevitably be skewed, and the following methods can be used:
```
SELECT *
FROM a
  LEFT JOIN b ON CASE
    WHEN a.user_id IS NULL THEN concat('hive_', rand())
    ELSE a.user_id
   END = b.user_id;
```
**Note:**
  The value randomly assigned to the null value should not be duplicated with the existing value in the table, otherwise the result will be wrong.
  
### 6. Limit adjustment optimization

Under normal circumstances, the Limit statement still needs to execute the entire query statement, and then return partial results.

There is a configuration property that can be turned on to avoid this: sampling the data source.
```
hive.limit.optimize.enable=true -- enable the function of sampling the data source

hive.limit.row.max.size -- set the minimum sampling size

hive.limit.optimize.limit.file -- set the maximum number of samples to sample
```
Disadvantage: it is possible that some data will never be processed  

### 7. JOIN optimization

1. Use the same connection key

When joining 3 or more tables, only one MapReduce job will be generated if each on clause uses the same join key.

2. Filter data as early as possible

Reduce the amount of data in each stage, add partitions to the partitioned table, and select only the fields that need to be used.

3. Try to be as atomic as possible

Try to avoid a SQL containing complex logic, you can use the intermediate table to complete the complex logic.

### 8. Predicate pushdown optimization
Predicate Pushdown in Hive is referred to as predicate pushdown. In short, it is to push the filter condition down before join as far as possible without affecting the result. After the predicate is pushed down, the filter conditions are executed on the map side, which reduces the output of the map side, reduces the amount of data transmitted on the cluster, saves the resources of the cluster, and improves the performance of the task.

Let's look at the following statement:
```
select s1.key, s2.key
from s1 left join s2
on s1.key > '2';
```
The above is a Left Join statement, s1 is the left table, called the reserved row table, and s2 is the right table.

Q: Is the s1.key > '2' of the on condition executed before or after the join? That is, will predicate pushdown be performed?

And the following statement:
```
select s1.key, s2.key
from s1 left join s2
on s2.key > '2';
```
The s2 table is not a reserved row, so the s2.key>2 condition can be pushed down to the s2 table, that is, executed before the join.

Look at the following sentence:
```
select s1.key, s2.key
from s1 left join s2
where s1.key > '2';
```
The right table s2 is a NULL supplementary table.

s1 is not a NULL supplementary table, so s1.key>2 can perform predicate pushdown.

And the following statement:
```
select s1.key, s2.key
from s1 left join s2
where s2.key > '2';
```
Since s2 is a NULL supplementary table, the s2.key>2 filter condition cannot be pushed down.

So what are the rules for predicate push-down, when will push-down be performed, and when will it not be pushed down? The following table is summarized, and it is recommended to save it as a collection:
* Answer: Predicate pushdown will not be performed, because s1 is a reserved row table, and the filter condition will be executed after join.

Case:
```
select a.*
from a
left join b on a.uid = b.uid
where a.ds='2020-08-10'
and b.ds='2020-08-10'
```
The above SQL mainly made two mistakes:

The where condition of the right table (the b table above) is written after the join, which will cause the entire table to be associated with the filter partition first.

**Note:**
  Although the where condition of the a table is also written after the join, the a table will push down the predicate, that is, execute the where condition first, and then execute the join, but the b table will not push down the predicate!

The condition of on does not filter null values. If there are a large number of null values in the two data tables, the data will be skewed.

### Code optimization principles:

* Understand the principle of demand, which is the foundation of optimization;
* Grasp the principle of full data link, which is the context of optimization;
* Adhere to the principle of concise code, which makes optimization easier;
* Talking about optimization when there are no bottlenecks is asking for trouble.
