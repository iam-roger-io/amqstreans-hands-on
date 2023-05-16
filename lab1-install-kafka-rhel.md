[Index](./index.md)

- [Download AMQ Streams - Red Hat Developer - Community ](https://developers.redhat.com/products/amq/download)
- [Download AMQ Streams - Enterprise ](https://access.redhat.com/downloads/)


**Descompactar os pacote baixado**
```
unzip amq-streams-2.3.0-bin.zip
```

**Descompactar o Apache Kafka somente para comparar com AMQ Streams**
```
tar zxvf kafka_2.13-3.3.1.tgz 
```

**Setar ambiente**
```
export KAFKA_HOME=/opt/kafka/kafka_2.13-3.3.1.redhat-00008
```

**Iniciar primeiro o Zookeeper**
```
$KAFKA_HOME/bin/zookeeper-server-start.sh $KAFKA_HOME/config/zookeeper.properties
```

**Iniciar o Kafka após iniciar Zookeeper**
```
$KAFKA_HOME/bin/kafka-server-start.sh $KAFKA_HOME/config/server.properties
```

```
vim ../config/server.properties 
```

```
vim ../config/zookeeper.properties
```

**Produtor**

```
$KAFKA_HOME/bin/kafka-console-producer.sh \
 --bootstrap-server localhost:9092 \
 --producer-property 'security.protocol=PLAINTEXT' \
 --topic my-topic
```

**Consumidor**
```
 $KAFKA_HOME/bin/kafka-console-consumer.sh \
 --bootstrap-server localhost:9092 \
 --consumer-property 'security.protocol=PLAINTEXT' \
 --topic my-topic --from-beginning
```

# Adicionar um novo broker

```
cp -r $KAFKA_HOME ./kafka2
```


No server.properties adicione um novo id para o broker

```
broker.id=1

Mude a porta
listeners=PLAINTEXT://:9093

Mude a pasta
log.dirs=/tmp/kafka-logs2

/opt/kafka/kafka2/bin/kafka-server-start.sh /opt/kafka/kafka2/config/server.properties

```
[Index](./index.md)