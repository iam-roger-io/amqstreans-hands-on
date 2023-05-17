#
# Zookeeper

[Index](./index.md)


Preparar nodes 

```
#!/bin/bash
#

rm -rfv /tmp/kafka
mkdir /tmp/kafka
mkdir /tmp/kafka/data

cd /tmp/kafka
mkdir /tmp/kafka/data/zookeeper1
mkdir /tmp/kafka/data/zookeeper2
mkdir /tmp/kafka/data/zookeeper3

# Preparar o arquivo zookeeper.properties base
cp  $KAFKA_HOME/config/zookeeper.properties /tmp/kafka/data/

echo initLimit=5 >> /tmp/kafka/data/zookeeper.properties
echo syncLimit=2 >> /tmp/kafka/data/zookeeper.properties
echo tickTime=2000 >> /tmp/kafka/data/zookeeper.properties
echo # list of servers >> /tmp/kafka/data/zookeeper.properties
echo server.1=0.0.0.0:2888:3888 >> /tmp/kafka/data/zookeeper.properties
echo server.2=127.0.0.1:2889:3889 >> /tmp/kafka/data/zookeeper.properties
echo server.3=127.0.0.1:2890:3890 >> /tmp/kafka/data/zookeeper.properties



# Preparar o primeiro nó de Zookeeper
touch /tmp/kafka/data/zookeeper1/myid
echo 1 >> /tmp/kafka/data/zookeeper1/myid
sed  's#dataDir=.*#dataDir=/tmp/kafka/data/zookeeper1#g' /tmp/kafka/data/zookeeper.properties >> /tmp/kafka/data/zookeeper1/zookeeper.properties

# Preparar o segundo nó de Zookeeper
touch /tmp/kafka/data/zookeeper2/myid
echo 2 >> /tmp/kafka/data/zookeeper2/myid
sed  's#clientPort=.*#clientPort=2182#g' /tmp/kafka/data/zookeeper.properties >> /tmp/kafka/data/zookeeper2/zookeeper.properties
sed  -i 's#dataDir=.*#dataDir=/tmp/kafka/data/zookeeper2#g' /tmp/kafka/data/zookeeper2/zookeeper.properties


# Preparar o terceiro nó de Zookeeper
touch /tmp/kafka/data/zookeeper3/myid
echo 3 >> /tmp/kafka/data/zookeeper3/myid
sed  's#clientPort=.*#clientPort=2183#g' /tmp/kafka/data/zookeeper.properties >> /tmp/kafka/data/zookeeper3/zookeeper.properties
sed  -i 's#dataDir=.*#dataDir=/tmp/kafka/data/zookeeper3#g' /tmp/kafka/data/zookeeper3/zookeeper.properties

# Manter logs separados
cp -r $KAFKA_HOME /tmp/kafka/zookeeper1
cp -r $KAFKA_HOME /tmp/kafka/zookeeper2
cp -r $KAFKA_HOME /tmp/kafka/zookeeper3

## Preparar o Kafka ajustando o arquivo server.properties
cp $KAFKA_HOME/config/server.properties /tmp/kafka/data/
sed -i "s/^[#]*\s*zookeeper.connect=.*/zookeeper.connect=localhost:2181,localhost:2182,localhost:2183/" /tmp/kafka/data/server.properties
sed  -i 's#log.dirs=.*#log.dirs=/tmp/kafka/data/kafka-logs#g' /tmp/kafka/data/server.properties

```

## Incie 3 Zookeepers

```
/tmp/kafka/zookeeper1/bin/zookeeper-server-start.sh /tmp/kafka/data/zookeeper1/zookeeper.properties
```

```
/tmp/kafka/zookeeper2/bin/zookeeper-server-start.sh /tmp/kafka/data/zookeeper2/zookeeper.properties
```

```
/tmp/kafka/zookeeper3/bin/zookeeper-server-start.sh /tmp/kafka/data/zookeeper3/zookeeper.properties
```


## Inicie o Kafka configurado para o cluster Zookeeper

```
$KAFKA_HOME/bin/kafka-server-start.sh /tmp/kafka/data/server.properties
```
Observe a lista de Zookeepers no arquivo [server.properties](/tmp/kafka/data/server.properties)


**Produtor**

```
$KAFKA_HOME/bin/kafka-console-producer.sh  \
--bootstrap-server localhost:9092  \
--producer-property 'security.protocol=PLAINTEXT'  \
--topic my-topic5
 
```

**Consumidor**

```
$KAFKA_HOME/bin/kafka-console-consumer.sh  \
--bootstrap-server localhost:9092  \
--consumer-property 'security.protocol=PLAINTEXT'  \
--topic my-topic5 \
--from-beginning
```


[Index](./index.md)