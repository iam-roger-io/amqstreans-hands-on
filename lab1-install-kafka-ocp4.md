[Index](./index.md)

- [Download AMQ Streams - Red Hat Developer - Community ](https://developers.redhat.com/products/amq/download)
- [Download AMQ Streams - Enterprise ](https://access.redhat.com/downloads/)
- [Download Apache Kafka](https://kafka.apache.org/downloads)


# Instalação Persistente no OCP

```
oc new-project amq-streams-lab
```

```
apiVersion: kafka.strimzi.io/v1beta2
kind: Kafka
metadata:
  name: amqstreams-lab
spec:
  kafka:
    version: 3.3.1
    replicas: 3
    listeners:
      - name: plain
        port: 9092
        type: internal
        tls: false
      - name: tls
        port: 9093
        type: internal
        tls: true            
    config:
      offsets.topic.replication.factor: 3
      transaction.state.log.replication.factor: 3
      transaction.state.log.min.isr: 2
      default.replication.factor: 3
      min.insync.replicas: 2
      inter.broker.protocol.version: "3.3"
    storage:
      type: jbod
      volumes:
      - id: 0
        type: persistent-claim
        size: 20Gi
        deleteClaim: false
  zookeeper:
    replicas: 3
    storage:
      type: persistent-claim
      size: 10Gi
      deleteClaim: false
  entityOperator:
    topicOperator: {}
    userOperator: {}
```

# Produzir e consumir no OCP

# Criar Topico

```
kind: KafkaTopic
apiVersion: kafka.strimzi.io/v1beta2
metadata:
  name: my-topic
  labels:
    strimzi.io/cluster: amqstreams-lab
  namespace: amq-streams-lab
spec:
  partitions: 10
  replicas: 3
  config:
    retention.ms: 604800000
    segment.bytes: 1073741824
```

## Interno 

**Producer**

Sem TLS
```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9092 \
 --producer-property 'security.protocol=PLAINTEXT' \
 --topic my-topic
```

Com TLS
```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9093 \
 --producer-property 'security.protocol=SSL' \
 --producer-property ssl.truststore.password=uvGd0SrNEWqf \
 --producer-property ssl.truststore.location=/opt/kafka/cluster-ca-certs/ca.p12 \
 --topic my-topic
 ```

 **Consumer**

Sem TLS
```
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9092 \
--consumer-property 'security.protocol=PLAINTEXT' \
--topic my-topic --from-beginning
```

Com TLS
```
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9093 \
--consumer-property 'security.protocol=SSL' \
--consumer-property ssl.truststore.password=uvGd0SrNEWqf \
--consumer-property ssl.truststore.location=/opt/kafka/cluster-ca-certs/ca.p12 \
--topic my-topic --from-beginning
```

## Adicionar acesso externo

Adicione o bloco abaixo aos listeners

```
      - name: external
        port: 9094
        type: route
        tls: true
```

oc project amq-streams-lab

```
oc extract secret/amqstreams-lab-cluster-ca-cert --keys=ca.crt --to=- > ca.crt
```

```
keytool -import -trustcacerts -alias root -file ca.crt -keystore truststore.jks -storepass password -noprompt
```

Producer
```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --producer-property 'security.protocol=SSL' \
 --producer-property ssl.truststore.password=password \
 --producer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --topic my-topic
 ```

 Consumer

 ```
 $KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --consumer-property 'security.protocol=SSL' \
 --consumer-property ssl.truststore.password=password \
 --consumer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --topic my-topic  \
 --from-beginning
 ```

Se quiser pode utilizar tmbem o ca.p12 com a senah que esta na secret.



[Index](./index.md)