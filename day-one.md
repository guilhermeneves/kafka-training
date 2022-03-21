# kafka-training day-one

Kappa Architecture: EDH - Enterprise Data Hub Kafka serving and processing data (KSQLDb) Batch get data from EDH (Hot storage and cold storage)

Lambda Architecture: two pipelines (batch and streaming) the issue here is to duplicate some scripts in both pipelines.


RabbitMQ: Distributed Message System

Pulsar: Streaming Platform (topics can be ephemeral or persistent)
- IO: source & sinks connectors
- Functions: LightWeight Compute Process
- SQL: Query using Trino


![](kafka-rabbit-pulsar.png)

Aiming big data / microservices and streaming Kafka is the most indicated.

If have only rabbitmq and system-system rabbitmq is the way.

Pulsar not very adopted yet.


Real time Data Ingestion

- Apache Kafka
- Azure Event Hubs
- Cloud Pub/Sub
- AWS Kinesis Data Streams
- AWS Kinesis Firehose

![](data-process.png)

Messaging system does not guarantee avoid duplicate messages. System developer needs to deduplicate in their end. Kafka can ensure deliver message just once, just need to configure it correctly.

99.999% kafka runs async although it can run sync.

## Apache Kafka Fundamentals

- Open Source - Scala & Java
- Storage & Processing System using a Distributed Transaction Log
- Developed by Linkedin
- Kafka Creators founded Confluent 
- Process Stream of Records [Kafka Stram & KSQL
- Core APIs: Producer API, Consumer API, Connector API, Streams API, KSQL API.

## Deployment Options

- Apache Kafka
- Amazon MSK: Managed Streaming for APache Kafka, current version 2.8.1
- Azure HDInsight: current version 2.2.1 not very used (legacy migrating to confluent)
- Confluent Platform & Operator: Kafka 3.0 / run on Confluent Kubernetes
- Strimzi: Apache Kafka on Kubernetes, Kafka version 3.1
- Confluent Cloud: Current Version = Latest, Re-Engineered for Cloud Computing, Expensive, but very easy all managed. Runs on Confluent Kubernetes

KIP 500 - Zookeper removal

### IaC and CM

- IaC
- Terraform
- Ansible
- Pulumi


## Strimzi Operator

Apache Kafka on Kubernetes, all Kafka Goodies.

- Scale on demand
- Observability - integrated prometheus and grafana
- Heavy Power with Fer resources

### Kubernetes

- K8S
- AKS (Azure)
- GKE (Gcloud)
- EKS (AWS)

Kubernetes initially is stateless it can be changed to stateful.

in the yaml storage type: ephemeral.


Starting by namespace, example ingestion.
Helm package will be installed on a namespace.


Storage type JBOD (just a bundle of disks)


There are 3 differente conf: JBOD, Disk ou Ephemeral

After heml we gonna apply the yaml config on the strimzi operator.

Kafka pods:
- Broker
- Zookepper
- Export (Metrics)



## Kafka Services Comparison

Same configuration

- AWS MSK: R$ 3.194/ month
- Azure HD Insight: R$ 4.149 / month
- Confluent Cloud: R$ 6.571 / month
- Strimzi Apache Kafka Operator: R$ 1.426 / month

## Kafka log Structure

All databases are log files in all cases disk saved.

Kafka can be consider database key-value.

Kafka escreve em disco sequencial e a HD nao acessa randomico e por isso mto rapido. Kafka registra binario (zero copy optimization)

### Topic

Topic is like a table, and partition is the way we distribute the write.

Topic is logic, partition data is immutable, sort is by partition, if you want to sort the topic you need to explicit set it.


Each partition have it offset that is a small block.

partition is to throughput improve writes.

### Log Compaction

Type = Event Data vs Keyed

Event Data

Keyed
- Record a State Change in an Entity
- Changelog of a database
- Capability to Replay a State of  source system

Log compaction: saves only the last snapshot, not the full track of logs.

Kafka is a pull architecture decoupled, consumers and producers does not affects.

## Replication

Guarantees Availability & Durability, replication is bounded to brokers. it must be multiple of # brokers.

Leaders communicate with the application and the followers does not communicate with the consumer and producers;

Out of sync follower that has not the same offset as the leader.

Log retention 7 days.

## Zookeeper

Coordinate and manage Broker.  Zookeper broker is called Assemble.

Controller: 1 per cluster regardless # of brokers. It elects Partition Leaders, Broker Elected as Controller, Creates an Ephemeral Node in apache Zookeeper. If you lose the controller it will take more time to Kafka to recover.

It avoids to scale linear. it uses disk-based, it get slower when having a lot of metadata different from kafka that write serial zookeeper do it randominc. QUanto mais particao mais metadata

KIP-500 replaces zookepper by a regular Kafka Broker. it's only develop yet. 

RAFT: Sistemas de tolerancia a falha, algoritmos de consensus, melhoria do pacsus

With Quorum Controller.