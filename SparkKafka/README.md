# Perform advanced streaming data transformations with Apache Spark and Kafka in Azure HDInsight

### Use Kafka to ingest real-time data

Apache Kafka can be used as a messaging system, a publish subscribe system, or as a streaming processing platform. It stores data to a log that can be read real-time, post processed, and can be read by multiple consumers at the same time.

### Use Spark structured streaming to process real-time events

Apache Spark is an open-source distributed data processing engine that can be used in many circumstances to quickly process, query, analyze, and transform data at scale. Spark is commonly used to perform ETL and SQL jobs on big data, processing streaming data, and performing machine learning.

Spark structured streaming has features unavailable on other platforms, such as fault tolerance guarantees, the ability to join batch and streaming data, the ability to handle late arriving data, and many more that we will learn more about later in this module. Also, with Azure HDInsight you can create a VNET that houses both your Spark and Kafka cluster. With both clusters in one VNET, the Spark cluster can access individual partitions of data within the Kafka cluster, instead of talking across clusters or endpoints. By providing access to individual partitions of data, you increase the parallelism you have in the real-time processing job, which gives you better throughput.

### Scenarios for using Kafka with Spark

There are many real-time analytics scenarios that would benefit from the real-time streaming that Apache Kafka and Spark enable. All these scenarios have both a **streaming data ingestion component**, which is the Kafka component, and an **analytical process running on the data**, which is the Spark component.

<img src="./2-img01.png" />

