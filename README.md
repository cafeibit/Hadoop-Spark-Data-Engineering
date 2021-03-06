# Use Hadoop & Spark to work on Data Engineering

## Hadoop Ecosystem

* <b>Hadoop Common</b> refers to the common utilities and libraries that support the other Hadoop modules. 
* <b>Hadoop Distributed File System (HDFS)</b> stores the data collected from the ingestion and distributes the data across multiple nodes. 
* <b>MapReduce</b> is used for making Big Data manageable by processing them in clusters.
* <b>Yet Another Resource Negotiator (YARN)</b> is the resource manager across clusters.
* The extended Hadoop Ecosystem consists of libraries or software packages that are commonly used with or installed on top of the Hadoop core. The Hadoop ecosystem is made up of components that support one another for Big Data processing.

### The four main stages of the Hadoop Ecosystem are Ingest, Store, Process / Analyze, and Access:

* Ingesting is the first stage of Big Data processing<br>
To get data from different sources and use tools like <b>Flume</b> and <b>Sqoop</b><br>
  * Flume and Sqoop are responsible for ingesting the data and transferring them to the Storage component, HDFS and <b>HBase</b>. Then, the data is distributed to a MapReduce framework like <b>Pig</b> and <b><a href="./Hive/README.md">Hive</a></b> to process and analyze the data, and the processing is done by parallel computing. After all that is done, tools like <b>Hue</b> are used to access the refined data.
   * Flume is a distributed service that collects, aggregates, and transfers Big Data to the storage system. Flume has a simple and flexible architecture based on streaming data flows and uses a simple extensible data model that allows for online analytic application.
   * Sqoop is an open-source product designed to transfer bulk data between relational database systems and Hadoop. Sqoop looks in the relational database and summarizes the schema. It then generates MapReduce code to import and export data as needed. Sqoop allows you to quickly develop any other MapReduce applications that use the records that Sqoop stored into HDFS.
    
* In the Store Data stage<br>
  * <b>HBase</b> is a column-oriented non-relational database system that runs on top of HDFS. It provides real time wrangling access to the Hadoop file system. HBase uses hash tables to store data in indexes and allow for random access of data, which makes lookups faster.
  * <b>Cassandra</b> is a scalable, NoSQL database designed to have no single point of failure.

* In the Analyze Process/Pipelines stage by <a href="./Airflow/">**Airflow**</a><br>
  * Airflow has a modular architecture and uses a message queue to orchestrate an arbitrary number of workers, ready to scale to infinity.
  * Airflow pipelines are defined in Python, allowing for dynamic pipeline generation. This allows for writing code that instantiates pipelines dynamically.
  * Easily define your own operators and extend libraries to fit the level of abstraction that suits your environment. 
  * Airflow pipelines are lean and explicit. Parametrization is built into its core using the powerful Jinja templating engine.
  * Airflow provides many plug-and-play operators that are ready to execute your tasks on Google Cloud Platform, Amazon Web Services, Microsoft Azure and many other third-party services. This makes Airflow easy to apply to current infrastructure and extend to next-gen technologies.
  * Monitor, schedule and manage your workflows via a robust and modern web application. No need to learn old, cron-like interfaces. You always have full insight into the status and logs of completed and ongoing tasks.
  
* In the Analyze Data stage
   * In the Analyze Data stage, Pig is used for analyzing large amounts of data.
   * Pig is a procedural data flow language and a procedural programming language that follows an order and set of commands. Pig is used for analyzing large amounts of data. Pig is a procedural data flow language and a procedural programming language that follows an order and set of commands.
   * Hive is used mainly for creating reports and operates on the server side of a cluster. Hive is a declarative programming language, which means it allows users to express which data they wish to receive.
  
* The final stage is ???Access data,??? where users have access to the analyzed and refined data.<br>
  * At this stage tools like <b>Impala</b> are often used. Impala is a scalable system that allows non-technical users to search and access the data in Hadoop.
  * And Hue is another tool of choice at this stage. Hue is an acronym for Hadoop user experience. Hue allows you to upload, browse, and query data. You can run Pig jobs and workflow in Hue. Hue also provides a SQL editor for several query languages like Hive, and MySQL.

## Spark Data Engineering

### Spark Basics

* Components of Spark

  * Master: manage cluster and nodes, not participatinging computing
  * Worker: computing node, process not participating in computing, report to master
  * Driver: running program's main method, createing object of spark context
  * Spark context: Controls the entire application lifecycle, including components such as the dagsheduler and task scheduler.
  * Client: User-submitted program entry.

* Spark works and advantages
         
  * Based on in-memory computing, reducing inefficient disk interactions;
  * Efficient scheduling algorithm, based on DAG;
  * Fault tolerance mechanism Linage, the essence is DAG and Lingae

  * How spark works

    After the user submits the job on the client side, the driver will run the main method and create the spark context. Execute the add operator to form a dag graph and input it to the dagscheduler, and divide the stages according to the dependencies between add and input the task scheduler. The task scheduler divides the stage into task sets and distributes them to the executors of each node for execution.

         ??? Build the running environment of the Application, and the Driver creates a SparkContext
         
         ??? SparkContext applies to the resource manager (Standalone, Mesos, Yarn) for Executor resources, and the resource manager starts the StandaloneExecutorbackend (Executor)
         
         ??? Executor applies for Task to SparkContext
         
         ??? SparkContext distributes the application to the Executor
         
         ??? SparkContext builds a DAG graph, DAGScheduler parses the DAG graph into stages, each stage has multiple tasks, forms a taskset and sends it to the task scheduler, and the task scheduler sends the task to the Executor to run
         
         ??? Task runs on Executor and releases all resources after running
         
* The principle of the active-standby switching mechanism of Spark

  * Master can actually be configured with two. Spark's native standalone mode supports master-standby switching. When the Active Master node hangs up, the Standby Master can be switched to the Active Master.
  * Spark Master master-standby switching can be based on two mechanisms, one is based on the file system, the other is based on ZooKeeper.
  * The active-standby switching mechanism based on the file system needs to manually switch to the Standby Master after the Active Master hangs up; while the Zookeeper-based active-standby switching mechanism can automatically switch the Master.

* Spark streaming and how it basically works

  * Spark streaming is an extension of the spark core API that can be used to process large-scale, high-throughput, fault-tolerant real-time data streams.
  * It supports reading data from a variety of data sources, such as Kafka, Flume, Twitter and TCP Socket, and can use operators such as map, reduce, join and window to process data, and the processed data can be saved to the file system, database waiting for storage.
  * The basic working principle of Spark streaming is: accept the real-time input data stream, and then split the data into batches, such as encapsulating the data collected every second into a batch, and then send each batch to the spark computing engine for processing, and finally A result data stream will be produced, and the data in it is also composed of batches one by one.

* There are several deployment modes of Spark, and what are the characteristics of each mode

  * Local mode

    Spark does not have to run in the hadoop cluster, it can be specified locally by starting multiple threads. Running Spark applications directly locally in a multi-threaded manner is generally for the convenience of debugging.

    There are three types of local mode
   
    --local : start only one executor
    --local[k] : start k executors
    --local[*] : start executors with the same number of cpus

   * Standalone mode

     Distributed deployment cluster with complete services, resource management and task monitoring are monitored by Spark itself, and this mode is also the basis of other modes.

   * Spark on yarn mode

     Distributed deployment of clusters, resource and task monitoring are managed by yarn, but currently only supports coarse-grained resource allocation, including cluster and client operation modes, cluster is suitable for production, driver runs on cluster sub-nodes, with fault tolerance, client is suitable for debugging, The driver runs on the client side.

    Describe the process by which Yarn performs a task
    
         1) The client client submits the Application to the ResouceManager, and the ResouceManager accepts the Application and selects a node according to the cluster resource status to start the Application's task scheduler driver (ApplicationMaster)
         2) The ResouceManager finds the node and commands the nodeManager on the node to start a new JVM process to run the driver (ApplicationMaster) part of the program. When the driver (ApplicationMaster) starts, it will first register with the ResourceManager, indicating that it is responsible for the running of the current program.
         3) The driver (ApplicationMaster) starts to download various resources such as related jar packages, and determines the specific resource content applied by the ResourceManager based on the downloaded jar and other information.
         4) After receiving the application from the driver (ApplicationMaster), the ResouceManager will satisfy the resource allocation request to the maximum extent, and send the metadata information of the resource to the driver (ApplicationMaster)
         5) After receiving the resource metadata information sent by the driver (ApplicationMaster), it will send an instruction to the NodeManager on the specific machine according to the metadata information to start the specific container.
         6) NodeManager receives the instruction from the driver and starts the container. After the container is started, it must register with the driver (ApplicationMaster).
         7) The driver (ApplicationMaster) receives the container's registration and starts to schedule and calculate tasks until the task is completed.
        
         **One detail to pay attention to:** 
         Note: If the ResourceManager fails to satisfy the resource request of the driver (ApplicationMaster) for the first time, and later finds that there are idle resources, it will actively send the metadata information of the available resources to the driver (ApplicationMaster) to provide more resources. for running the current program.
    
     What are the advantages of Spark on Yarn mode
     
         1) Share cluster resources with other computing frameworks (Spark framework and MapReduce framework run at the same time, if Yarn is not used for resource allocation, MapReduce will allocate very few memory resources, which is inefficient); resources are allocated on demand, thereby improving cluster resource utilization, etc.
         2) Compared with the Standalone mode that comes with Spark, Yarn's resource allocation is more detailed.
         3) Application deployment is simplified. For example, after applications of various frameworks such as Spark and Storm are submitted by the client, Yarn is responsible for resource management and scheduling, using Container as a unit of resource isolation, and using it as a unit to use memory, cpu, etc.
         4) Yarn manages multiple services running in the Yarn cluster at the same time by means of queues, and can adjust the corresponding resource usage according to different types of application load conditions to achieve resource elastic management. 
      
      About the container
      
         1) Container is the basic unit of resource allocation and scheduling, which encapsulates resources such as memory, CPU, disk, network bandwidth, etc. Currently yarn only encapsulates memory and CPU.
         2) The Container is applied by the ApplicationMaster to the ResourceManager, and is asynchronously allocated to the ApplicationMaster by the resource scheduler in the ResouceManager
         3) The operation of the Container is initiated by the ApplicationMaster to the NodeManager where the resource is located, and the Container needs to provide internal execution task commands when it is running.
      
   * Spark On Mesos mode

     This model is officially recommended (of course, one of the reasons is blood relationship). It is precisely because Spark was developed with Mesos in mind, so now, Spark running on Mesos will be more flexible and more natural than running on YARN. Users can choose one of two scheduling modes to run their applications:
    
     --Coarse-grained Mode: The running environment of each application consists of a Dirver and several Executors. Each Executor occupies several resources and can run multiple Tasks (corresponding to how many "slots"). Before each task of the application program is officially run, all resources in the running environment need to be applied for, and these resources must be occupied during the running process.

     --Fine-grained Mode: Since the coarse-grained mode will cause a lot of waste of resources, Spark On Mesos also provides another scheduling mode: the fine-grained mode, which is similar to the current cloud computing, the idea is on-demand distribute.

* The similarities and differences between hadoop and spark's shuffle
  
  * From a high-level perspective, there is no big difference between the two. Both partition the output of mapper (ShuffleMapTask in Spark), and send different partitions to different reducers (reducer in Spark may be ShuffleMapTask in the next stage, or ResultTask). Reducer uses memory as a buffer, aggregates data while shuffled, and performs reduce() after data is aggregated (in Spark may be a series of subsequent operations).
  
  * From a low-level point of view, the difference between the two is not small. Hadoop MapReduce is sort-based, and records entering combine() and reduce() must be sorted first. The advantage of this is that combine/reduce() can handle large-scale data, because its input data can be obtained through outflow (mapper sorts each piece of data first, and reducer's shuffle merges each sorted piece of data). The current Spark default selection is hash-based, usually HashMap is used to aggregate the shuffled data, and the data will not be sorted in advance. If users need sorted data, they need to call operations like sortByKey() themselves; if you are a Spark 1.1 user, you can set spark.shuffle.manager to sort, and the data will be sorted. In Spark 1.2, sort will be the default Shuffle implementation.
  
  * From an implementation point of view, there are many differences between the two. Hadoop MapReduce divides the processing flow into distinct stages: map(), spill, merge, shuffle, sort, reduce(), etc. Each stage has its own responsibilities, and the functions of each stage can be realized one by one according to the procedural programming idea. In Spark, there is no such clearly functional stage, only different stages and a series of transformation(), so operations such as spill, merge, aggregate need to be implied in transformation(). If we call the process of dividing data and persisting data on the map side as shuffle write, and the process of reading data into reducer and aggregate data as shuffle read. So in Spark, the question becomes how to add the processing logic of shuffle write and shuffle read to the logical or physical execution graph of the job? And how should the two processing logic be implemented efficiently? Shuffle write Because the data is not required to be ordered, the task of shuffle write is very simple: partition the data well and make it persistent. The reason for persistence is to reduce memory storage space pressure on the one hand, and fault-tolerance on the other hand.

  * Spark's shuffle process can be expanded from the following three points:
         1) The division of the shuffle process
         2) How to store intermediate results of shuffle
         3) How to pull data from shuffle

* How to optimize Spark

  * Spark tuning is more complicated, but it can be roughly divided into three aspects:

         1) Tuning at the platform level: prevent unnecessary jar package distribution, improve data locality, and select efficient storage formats such as parquet

         2) Application-level tuning: The optimization of filter operators reduces too many small tasks, reduces the resource overhead of a single record, handles data skew, reuses RDD for caching, and executes jobs in parallel, etc.

         3) Tuning at the JVM level: set the appropriate amount of resources, set a reasonable JVM, enable efficient serialization methods such as kyro, increase off head memory, etc.

  * Where is data locality determined
    --Which machine the specific task runs on is determined when the dag divides the stages
    
  * What is the elasticity of RDD
  
         1) Automatically switch between memory and disk storage;

         2) Efficient fault tolerance based on Lineage;

         3) If the task fails, it will automatically retry a certain number of times;

         4) If the stage fails, it will automatically retry a certain number of times, and only the failed shards will be calculated;

         5) checkpoint and persist, persistent cache after data calculation;

         6) Data scheduling flexibility, DAG TASK scheduling has nothing to do with resources;

         7) High elasticity of data sharding.    

  * What are the pitfalls of RDDs
 
          1) Fine-grained write and update operations (such as web crawlers) are not supported, and spark writes data in coarse-grained terms. The so-called coarse-grained is to write data in batches, in order to improve efficiency. However, reading data is fine-grained, which means that it can be read one by one.

          2) Incremental iterative calculation is not supported, but Flink does

  * What are the types of data locality in Spark
    
    There are three types of data locality in Spark:

          1) PROCESS_LOCAL refers to reading data cached on the local node
    
          2) NODE_LOCAL refers to reading the local node hard disk data
    
          3) ANY refers to reading non-local node data
    
    Usually read data PROCESS_LOCAL>NODE_LOCAL>ANY, try to read data in PROCESS_LOCAL or NODE_LOCAL mode. Among them, PROCESS_LOCAL is also related to the cache. If the RDD is often used, the RDD is cached into the memory. Note that since the cache is lazy, it must be triggered by an action to actually cache the RDD into the memory.
    
   * Why does Spark persist, and in what scenarios should the persist operation be performed? Why Persistence?

     All the more complex algorithms of spark will have a persistent figure. The default data of spark is stored in memory, and many contents of spark are stored in memory, which is very suitable for high-speed iteration. There is only the first input data in 1000 steps, and no temporary data is generated in the middle, but Distributed systems are very risky, so they are prone to errors and must be fault-tolerant. RDD errors or sharding can be calculated based on lineage. If the parent RDD is not persisted or cached, it needs to be done all over again. Normally, persist is used in the following scenarios:

         1) The calculation of a certain step is very time-consuming and needs to be persisted
    
         2) The calculation chain is very long, and it takes a lot of steps to restore it, which is very easy to use, persist
    
         3) The rdd where the checkpoint is located should be persisted persist. Before checkpointing, it is necessary to persist, write rdd.cache or rdd.persist, save the result, and then write the checkpoint operation, which will execute very fast, and there is no need to recalculate the rdd chain. persist before checkpoint.
   
         4) After shuffle, persist, shuffle requires network transmission, the risk is very high, the data is lost again, and the recovery cost is very high

         5) Persist before shuffle, the framework will persist data to disk by default, this is done automatically by the framework 

  * Introduce the join operation optimization experience?
          
          In fact, there are two common types of join: map-side join and reduce-side join. When joining large and small tables, using map-side joins can significantly improve efficiency. Associating multiple pieces of data is a very common usage in data processing, but in distributed computing systems, this problem often becomes very troublesome, because the join operation provided by the framework generally sends all data to all the data according to the key. Go to the reduce partition, which is the process of shuffle. Causes a lot of network and disk IO consumption, and the operation efficiency is extremely low. This process is generally called reduce-side-join. If one of the tables is small, we can implement data association on the map side by ourselves, skip the process of shuffling a large amount of data, and the running time is greatly shortened. Depending on the data, the performance may be several times to dozens of times promote.
          
### <a href="./SQL/">SparkSQL</a>
