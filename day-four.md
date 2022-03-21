# Day 4 - Kafka Training

### **Fluxo Dados Streaming**

Producers geram dados no tópico especifico (apps, databases sql & nosql, etc...) (Não utilizar dois producers para mesmo tópico *Recomendação!) tópico ~= tabela, defina um propósito)

Enriquecimento de dados é feito no próprio cluster, de um tópico para outro.

- KSQLdb
- Fautst
- Spark Struct Streaming
- Flink

### **Como disponibilizar esses dados nas pontas?**

## Consumer

- Earliest: Do começo ao fim (Tudo)
- Latest: Somente o diferencial (do ultimo lido)

Kafka store messages in binary, when reading (consume) needs to deserialize to read.

- Librdkafka is a C library of the apache kafka protocol, providing producer, consumer & admin clients.

Producers = 1 million messages
Consumer = 3 million messages

## Consumer Group

Todas as aplicações do mesmo consumer group são tratados como uma mesma aplicação pelo Kafka.

Cada Consumer Group é uma aplicação! Se você tem uma aplicação spark lendo e uma aplicação Faust lendo, são dois consumer groups e não dois consumers dentro do mesmo consumer group.

Se existe apenas 1 aplicação no consumer group este vai ler de todas as partições e pode ter um problema de fall behind.

Para evitar este problema é possível apenas adicionar mais uma aplicação de consumer no mesmo consumer group, que o kafka irá distribuir o consumo das partições no cluster, e isso torna o consumer escalável.

**Se tiver mais consumidores do que partições no tópico no Kafka o excedente de consumidores ficará IDLE, pois o número de consumidores máx (thread disponíveis) é relacionado ao número de partições no tópico.**

## Kafka Consumer [Dissecting read Process]

Kafka is a pull architecture, consumer and producer are resposbles of propagate messages, not Kafka.

1. Initial Config

    - Bootstrap Servers: address ips
    - Key.Deserializer
    - Value.Deserializer

    Recommendation: always use Group.Id

2. Topic Subscription

    subscripe to one or more topics

    - Topic Name

3. Poll Loop
    the heart of the consumer API is a simple loop for polling data, it handles automatically

    - Coordination
    - Partition rebalance
    - heartbeat
    - data fetching
    infinite loop = long running app that polls from kafka
    poll() = needs to keep polling data otherwise will be considered dead.


`__consumer_offsets: ` duplo underscore topico de sistema

- marshmellow: lib para simplificar acesso a schemas.


## Rebalance (Kafka Consumer Groups & Partition Rebalance)

moving partition ownerniship from one consumer to another is called rebalance. Se você perde uma thread de um consumidor você tem um período de indisponibilidade.

Acontece em 3 casos: (normalmente ms, mas exponencial em relação ao numero de partições.) Se o consumer está intermitente ele fica dando rebalance toda hora.

1. Adicionar um novo consumer no consumer group. 
2. Consumer Shuts Down or Crashes
3. Topic is Modified = New Partitions Added (All consumer group got stop)
  
## Kafka Consumer Groups & Membership

Consumer group Leader that communicates with the Broker Group Coordinator.

HeartBeat: Healthcheck each consumer sends to the Cluster Group Coordinator and the coordinator sends to the leader that one of the consumers is dead to remove it.


## Kafka COnsumer Groups [Commits & Offsets]

- Commits & Offsets

**Commit** é a forma que o consumer tem de comunicar para o CLuster que a mensagem foi recebida pelo consumer, a forma de fazer o update da posição atual no topico é o commit

**Offset** It produces a message to kafka, to a special_consumer_offset topic with the commited offset for each partition
O consumer usa o kafka para rastrear a posição.

Se você estiver usando consumer que garantem EOS: Samza, Spark, Flink, Faust, etc... não precisa se preocupar com os problemas de reprocessing e mixed event, pois é garantido pelo EOS o tratamento desses problemas.
![](consumer-eos.png)

- By Default Kafka consumer Groups [Commit Offset Options] is Automatic Commit [Enable.Auto.Commit=True] which can have *data Loss*: Every five seconds the consumer will commit the largest offset your client received from poll(). The setting can be controle by Auto.Commit.interval.ms
    - it does not give to developer enough control to avoid duplicated

    - tou can disable the autocommit, although you will limit the throughput in your application

One option is **Async Commit**: it's faster but does not allow retry, if the commit fails can leads to duplicated messages.

None of these 3 options solve the issue, one recomendation is to combine sync and async:

![](commit_options.png)


