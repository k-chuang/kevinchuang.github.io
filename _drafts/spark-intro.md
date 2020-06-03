---
title: Spark Introduction
# date: 2020-06-02 15:44:18.000000000 -07:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- General
- Distributed Computing
- Apache Spark
- Kubernetes
- Google Dataproc
- Hadoop
tags:
- Apache Spark
- PySpark
- YARN
- HDFS
- Google Dataproc
- Hadoop
# meta:
  # _wpcom_is_markdown: '1'
  # _publicize_job_id: '30813365882'
  # timeline_notification: '1557873861'
# permalink: "/2020/06/02/pyspark-introduction/"
---

# Apache Spark Introduction
Apache Spark is a fast and general-purpose cluster computing system. It provides high-level APIs in Java, Scala, Python and R, and an optimized engine that supports general execution graphs. It also supports a rich set of higher-level tools including **Spark SQL** for SQL and structured data processing, **MLlib** for machine learning, **GraphX** for graph processing, and **Spark Streaming**.

Spark is a great tool to have as a data engineer/ data scientist / machine learning engineer, because it can handle large amounts of data (yuck Big Data) and is very fast at processing data due distributed computing (where tasks are separated out to multiple computers) and in-memory data structures.

# Distributed Computing Terms
- **Partitioned Data**
  - Because the processing tasks will be divided across multiple nodes, the data also has to be able to be divided across multiple nodes. Partitioned data refers to data that has been optimized to be able to be processed on multiple nodes. Spark's RDDs are partitioned.
    - Dataframes can be partitioned on `user_id`, which is similar to indexing in relational databases. Example is listed below. N = Node and U = user.
      - N1: U1, U4, U6
      - N2: U2, U3, U7
- **Fault Tolerance**
  - Refers to the ability of a system to continue working properly in the event of a failure (e.g. node failure or communication break between nodes). Spark has RDDs (i.e. Resilient Distributed Datasets), which are fault-tolerant, because they are essentially DAGs (i.e. Directed Acyclic Graphs) of computation that can be recomputed when there's a failure.
- **Lazy Evaluation**
  - Lazy evaluation has to do with how code is compiled and executed. Lazy evaluation is the opposite of strict / eager / greedy evaluation, which continually & eagerly processes code line by line (how typical code runs). An example of lazy evaluation is how Python generators work (lazy evaluation until .next is called). Spark programs are lazily evaluated via transformations, and only when an action is called (e.g. display result or save output), will the program actually run.
    - Lazily evaluated transformation: `df.select("user_id").join(df_2, on="user_id")`
    - Action will trigger compilation and execution of code.
      - `df.collect()`

# Apache Spark Terms
- **Resilient Distributed Datasets (RDDs)**
  - a fundamental data structure of Spark and an immutable distributed collection of objects. 
  - Each dataset in RDD is divided into logical partitions, which may be computed on different nodes in the cluster
  - Resilient = they are resilient due to their immutability and lineage graphs (which will be discussed shortly)
  - Distributed = partitions in multiple nodes
  - RDD is a read-only, partitioned collection of records. RDDs can be created through deterministic operations on either data on stable storage or other RDDs. RDD is a fault-tolerant collection of elements that can be operated on in parallel.
  - RDDs support in-memory processing computation. This means, it stores the state of memory as an object across the jobs and the object is sharable between those jobs. Data sharing in memory is 10 to 100 times faster than network and Disk.
- **Spark DataFrame**
  - Abstraction on top of RDDs
  - Have all features of RDDs, but also a schema
  - Typically use DataFrames instead of RDDs directly.
  - DataFrames are just organized into a columnar structure.
  - Similar to a table in a relational database
  - More optimized than RDDs
    - Optimization takes place using catalyst optimizer. Dataframes use catalyst tree transformation framework in four phases: a) Analyzing a logical plan to resolve references. b) Logical plan optimization. c) Physical planning. d) Code generation to compile parts of the query to Java bytecode.
- **Transformations**'
  - They are lazy operations that can be applied to RDDs to create one or more new RDDs. It’s important to note that Transformations create new RDDs because, remember, RDDs are immutable so they can’t be altered in any way once they’ve been created.
  - Transformations take an RDD as an input and perform some function on them based on what Transformation is being called, and outputs one or more RDDs.
  - As a compiler comes across each Transformation, it doesn’t actually build any new RDDs, but rather constructs a chain of hypothetical RDDs that would result from those Transformations which will only be evaluated once an Action is called.
  - This chain of hypothetical, or “child”, RDDs, all connected logically back to the original “parent” RDD, is what a lineage graph is.
- **Actions**
  - An Action is any RDD operation that does not produce an RDD as an output. Some examples of common Actions are doing a count of the data, or finding the max or min, or returning the first element of an RDD, etc. As was mentioned before, an Action is the cue to the compiler to evaluate the lineage graph and return the value specified by the Action.
- **Lineage Graph**
  - a lineage graph outlines what is called a “logical execution plan”
  -  What that means is that the compiler begins with the earliest RDDs that aren’t dependent on any other RDDs, and follows a logical chain of Transformations until it ends with the RDD that an Action is called on.

# References
- https://www.tutorialspoint.com/apache_spark/apache_spark_rdd.htm
- https://towardsdatascience.com/a-neanderthals-guide-to-apache-spark-in-python-9ef1f156d427