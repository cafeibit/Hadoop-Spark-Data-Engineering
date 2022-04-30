# Use Hadoop & Spark to work on Data Engineering

## Master Hadoop Ecosystem

* <b>Hadoop Common</b> refers to the common utilities and libraries that support the other Hadoop modules. 
  * <b>Hadoop Distributed File System (HDFS)</b> stores the data collected from the ingestion and distributes the data across multiple nodes. 
* <b>MapReduce</b> is used for making Big Data manageable by processing them in clusters.
* <b>Yet Another Resource Negotiator (YARN)</b> is the resource manager across clusters.

The extended Hadoop Ecosystem consists of libraries or software packages that are commonly used with or installed on top of the Hadoop core. The Hadoop ecosystem is made up of components that support one another for Big Data processing.
* <b>Flume and Sqoop</b> are responsible for ingesting the data and transferring them to the Storage component, <b>HDFS and HBase</b>. Then, the data is distributed to a MapReduce framework like <b>Pig and Hive</b> to process and
analyze the data, and the processing is done by parallel computing. After all that is done, tools like <b>Hue</b> are used to access the refined data.
