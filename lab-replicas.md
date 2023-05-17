#
# Replication Factor

[Index](./index.md)

```
/tmp/kafka/lab-replicas/broker1/bin/zookeeper-server-start.sh /tmp/kafka/lab-replicas/broker1/config/zookeeper.properties 
```

```
/tmp/kafka/lab-replicas/broker1/bin/kafka-server-start.sh /tmp/kafka/lab-replicas/broker0/config/server.properties 
```

```
/tmp/kafka/lab-replicas/broker1/bin/kafka-server-start.sh /tmp/kafka/lab-replicas/broker1/config/server.properties 
```

```
/tmp/kafka/lab-replicas/broker1/bin/kafka-server-start.sh /tmp/kafka/lab-replicas/broker2/config/server.properties 
```

```
$KAFKA_HOME/bin/kafka-topics.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094  \
--create  \
--topic lab-replicas \
--partitions 3 \
--replication-factor 3
```

```
$KAFKA_HOME/bin/kafka-topics.sh  \
 --bootstrap-server localhost:9092,localhost:9093,localhost:9094  \
 --describe \
 --topic lab-replicas
```

$KAFKA_HOME/bin/kafka-console-producer.sh \
--bootstrap-server localhost:9092,localhost:9093,localhost:9094  \
--topic lab-replicas 

[Index](./index.md)