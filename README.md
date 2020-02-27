# Kafka Streams Quarques Kogito Project


The *alert* topic listens to incoming feeds of harvest count from edge device. The disaster monitor application will monitor all the input and sum up the total incoming counts, if the total harvest count is less then 150 every 10 sec, it will need to notify users in the *disaster* channel (topic)


****
edge (JSON : harvesevent) 

-> alert(kafka topic) 

-> OUR APP (if sum of harvest count > 150 every 10 sec) 

-> disaster=true/false(kafka topic)
****

##Install and run Kafka

Run zookeeper

```
bin/zookeeper-server-start.sh config/zookeeper.properties
```

Run kafka 

```
bin/kafka-server-start.sh config/server.properties
```
Create two topics needed

```
bin/kafka-topics.sh --create --zookeeper localhost:2181     --replication-factor 1 --partitions 1 --topic alert
bin/kafka-topics.sh --create --zookeeper localhost:2181     --replication-factor 1 --partitions 1 --topic disaster
```

##Start Application

Start the application

```
mvn clean package quarkus:dev
```

In another CMD console start a producer to send harvest data (***HARVEST SENDER***)

```
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic alert
```

In another CMD console start a producer to recieve disaster alert

```
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic disaster
```


##Send In data 

In ***HARVEST SENDER*** send in the following data

```
{"batchtime":1582661010246, "batchcnt":6, "farmid":101}

{"batchtime":1582661015283, "batchcnt":78, "farmid":302}

{"batchtime":1582661020246, "batchcnt":57, "farmid":645}
```

If batch count sent into alert topic, add up more than 150, no disaster.

Should get something similar as follows. 

```
{"totalCnt":702,"disaster":false}
{"totalCnt":156,"disaster":false}
{"totalCnt":78,"disaster":true}
{"totalCnt":78,"disaster":true}
```
