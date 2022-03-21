# Day-3

## StreamingSQL Engines

It's a distributed processing engine acts as a processor, (job manager, task manager) process the data received in one topic to deliver to the sink or a consumer in another topic, for example using Kafka Connect. It has its own cluster for the processing enginer apart from kafka,

- Apache Flink (FlinkSQL - 2016)
- Apache Spark (Structure Streaming - 2016)
- Apache Kafka (KSQL - 2017)
- Apache Beam (Beam SQL - 2017)

Apache Flink: 

- Ensure EOS (exactly once semantics)
- Stateful COmputations - Bounded (defined window like last 10 minutes) & Unbounded (undefined window runs undefinitely)

Datasets (Batch Process)
DataStream (Stream Process)

## Kafka Streams

Lib written java/scala lightweitght.
Has EOS event by event process, Decoupled Processing Engine (JVM)
High Level Stream DSL
Low Level Processor API

Consume / Process/ Produce

it runs as an application, it's recommended to use in a differente machine from the cluster.

Global KTable, KTable, KStream.

KTable: Just the actual table state
KStream: All sequence data

Stateful: processing of message is independent
Stateless:

Needs to create an object (Ktable or Kstreams) before run a query

## KSQLDB (Single Mental Model)

With KSQLDB we can simplify data pipeline using as a Database on top of apache kafka. It's a deployment option on top of the Kafka Cluster.

- Streaming ETL
- Realmtime MOnitoring
- Data Exploration
- Sensor DAta & IoT


Cli is open, UI is paid.

KSQL Engine = Executes KSQL Statements & Queries {Kafka Stream API}
KSQL Rest Interface = Server Interface for COmmunications to KSQL Engine

Deployment Models:

Interactive = data exploration
Headless = long running production environment

General [Production- Size]

CPU = COnsume to SerDe Stream and Tables [4 cores]
Disk Persist Temporary State for Agg & Joins[ssd 100gb]
Memory State and join operations 32gb
Network 1 gbit

Language Elements

DDL
DML

![](kstream.png)


Needs to create an object (Ktable or Kstreams) before run a query

## Apache Spark [Structured Streaming]

Spark process batch by batch (micro batch)
Built on Spark SQL Engine
Project Tungsten [CPU & Memory]
Optmize using Catalyst Optimizer

- Seamless

Express Streaming Computation = Batch Computation

Philosophy Treat Data Streams = Unbounded Tables. (Incremental Query over Streams)

Depends on the use-case each of the process engine outperforms;.

Source -> spark.readStream.format("kafka).option("subscribe", "input").load()

Transformation -> groupBy

Sink -> writeStream.format("kafka").option("topic", "output")

Trigger -> .trigger("1 minute".outputMode("upddate")) **Ao inves de deixart ligado o streaming pode ler de 1/1min

Checkpoint -> .option("checkpointLocation", "...").start()

Streaming generally uses input topic (source) and output topic (Transformed topic)

## Spark

-> Init Spark Session

-> Set log level

-> event Schema


Spark for batch using read does not have option to set startingOffsets to latest. Kafka will need a logic to remove the data to avoid read data from the beggining.

watermark window spark!

## Faust

Uses celery to scale python.
It's application similar to kafka stream.
Exactly Once Semantics

Requisites

- Python 3.6
- Apache Kafka

- Agents
- Tables
- Fast
- Highly Available
- Flexible

![](faust.png)

## Comparison

![](comparison.png)