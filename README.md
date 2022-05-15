# Use Hadoop & Spark to work on Data Engineering

## Master Hadoop Ecosystem

* <b>Hadoop Common</b> refers to the common utilities and libraries that support the other Hadoop modules. 
* <b>Hadoop Distributed File System (HDFS)</b> stores the data collected from the ingestion and distributes the data across multiple nodes. 
* <b>MapReduce</b> is used for making Big Data manageable by processing them in clusters.
* <b>Yet Another Resource Negotiator (YARN)</b> is the resource manager across clusters.
* The extended Hadoop Ecosystem consists of libraries or software packages that are commonly used with or installed on top of the Hadoop core. The Hadoop ecosystem is made up of components that support one another for Big Data processing.

### The four main stages of the Hadoop Ecosystem are Ingest, Store, Process and Analyze, and Access:

* Ingesting is the first stage of Big Data processing<br>
To get data from different sources and use tools like <b>Flume</b> and <b>Sqoop</b><br>
  * Flume and Sqoop are responsible for ingesting the data and transferring them to the Storage component, HDFS and <b>HBase</b>. Then, the data is distributed to a MapReduce framework like <b>Pig</b> and <b>Hive</b> to process and analyze the data, and the processing is done by parallel computing. After all that is done, tools like <b>Hue</b> are used to access the refined data.
   * Flume is a distributed service that collects, aggregates, and transfers Big Data to the storage system. Flume has a simple and flexible architecture based on streaming data flows and uses a simple extensible data model that allows for online analytic application.
   * Sqoop is an open-source product designed to transfer bulk data between relational database systems and Hadoop. Sqoop looks in the relational database and summarizes the schema. It then generates MapReduce code to import and export data as needed. Sqoop allows you to quickly develop any other MapReduce applications that use the records that Sqoop stored into HDFS.
    
* In the Store Data stage<br>
  * <b>HBase</b> is a column-oriented non-relational database system that runs on top of HDFS. It provides real time wrangling access to the Hadoop file system. HBase uses hash tables to store data in indexes and allow for random access of data, which makes lookups faster.
  * <b>Cassandra</b> is a scalable, NoSQL database designed to have no single point of failure.

* In the Analyze Process/Pipelines stage by **Airflow**<br>
  * Airflow has a modular architecture and uses a message queue to orchestrate an arbitrary number of workers, ready to scale to infinity.
  * Airflow pipelines are defined in Python, allowing for dynamic pipeline generation. This allows for writing code that instantiates pipelines dynamically.
  * Easily define your own operators and extend libraries to fit the level of abstraction that suits your environment. 
  * Airflow pipelines are lean and explicit. Parametrization is built into its core using the powerful Jinja templating engine.
  * Airflow provides many plug-and-play operators that are ready to execute your tasks on Google Cloud Platform, Amazon Web Services, Microsoft Azure and many other third-party services. This makes Airflow easy to apply to current infrastructure and extend to next-gen technologies.
  * Monitor, schedule and manage your workflows via a robust and modern web application. No need to learn old, cron-like interfaces. You always have full insight into the status and logs of completed and ongoing tasks.
  
* In the Analyze Data stage<br>
   * In the Analyze Data stage, Pig is used for analyzing large amounts of data.
   * Pig is a procedural data flow language and a procedural programming language that follows an order and set of commands. Pig is used for analyzing large amounts of data. Pig is a procedural data flow language and a procedural programming language that follows an order and set of commands.
   * Hive is used mainly for creating reports and operates on the server side of a cluster. Hive is a declarative programming language, which means it allows users to express which data they wish to receive.
  
* The final stage is “Access data,” where users have access to the analyzed and refined data.<br>
  * At this stage tools like <b>Impala</b> are often used. Impala is a scalable system that allows non-technical users to search and access the data in Hadoop.
  * And Hue is another tool of choice at this stage. Hue is an acronym for Hadoop user experience. Hue allows you to upload, browse, and query data. You can run Pig jobs and workflow in Hue. Hue also provides a SQL editor for several query languages like Hive, and MySQL.

## Master Spark Data Engineering

### Spark Basics

* Components of Spark

  * Master: manage cluster and nodes, not participatingin computing
  * Worker: computing node, process not participating in computing, report to master
  * Driver: running program's main method, createing object of spark context
  * Spark context: Controls the entire application lifecycle, including components such as the dagsheduler and task scheduler.
  * Client: User-submitted program entry.

* How Spark works
         
  * After the user submits the job on the client side, the driver will run the main method and create the spark context. 
  * Execute the add operator to form a dag graph and input it to the dagscheduler, 
  * and divide the stages according to the dependencies between add and input the task scheduler. 
  * The task scheduler divides the stage into task sets and distributes them to the executors of each node for execution.

* Spark streaming and how it basically works

  * Spark streaming is an extension of the spark core API that can be used to process large-scale, high-throughput, fault-tolerant real-time data streams.
  * It supports reading data from a variety of data sources, such as Kafka, Flume, Twitter and TCP Socket, and can use operators such as map, reduce, join and window to process data, and the processed data can be saved to the file system, database waiting for storage.
  * The basic working principle of Spark streaming is: accept the real-time input data stream, and then split the data into batches, such as encapsulating the data collected every second into a batch, and then send each batch to the spark computing engine for processing, and finally A result data stream will be produced, and the data in it is also composed of batches one by one.

* There are several deployment modes of Spark, and what are the characteristics of each mode

 * Local mode

   Spark does not have to run in the hadoop cluster, it can be specified locally by starting multiple threads. Running Spark applications directly locally in a multi-threaded manner is generally for the convenience of debugging.

   There are three types of local mode

   --local : start only one executor
     local[k] : start k executors
     local[*] : start executors with the same number of cpus

  * Standalone mode

        Distributed deployment cluster with complete services, resource management and task monitoring are monitored by Spark itself, and this mode is also the basis of other modes.

3) Spark on yarn mode

        Distributed deployment of clusters, resource and task monitoring are managed by yarn, but currently only supports coarse-grained resource allocation, including cluster and client operation modes, cluster is suitable for production, driver runs on cluster sub-nodes, with fault tolerance, client is suitable for debugging, The driver runs on the client side.

4) Spark On Mesos mode

        This model is officially recommended (of course, one of the reasons is blood relationship). It is precisely because Spark was developed with Mesos in mind, so now, Spark running on Mesos will be more flexible and more natural than running on YARN. Users can choose one of two scheduling modes to run their applications:

Coarse-grained Mode: The running environment of each application consists of a Dirver and several Executors. Each Executor occupies several resources and can run multiple Tasks (corresponding to how many "slots"). Before each task of the application program is officially run, all resources in the running environment need to be applied for, and these resources must be occupied during the running process.

Fine-grained Mode: Since the coarse-grained mode will cause a lot of waste of resources, Spark On Mesos also provides another scheduling mode: the fine-grained mode, which is similar to the current cloud computing, the idea is on-demand distribute.
