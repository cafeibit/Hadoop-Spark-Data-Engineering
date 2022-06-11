# Hive Basic

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


