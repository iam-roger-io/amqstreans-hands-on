## Crie um tópico com 2 partições (use um dos métodos abaixo)

Criar através da CLI 
```
$KAFKA_HOME/bin/kafka-topics.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443  \
--command-config /tmp/kafka/command.properties \
--create  \
--topic consumer-group-lab \
--partitions 2
```

Criar tópico através do CRD (yaml via operator)

```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaTopic
metadata:
  name: consumer-group-lab
  labels:
    strimzi.io/cluster: amq-streams-lab
  namespace: amq-streams-lab
spec:
  partitions: 2
  replicas: 3
  config:
    retention.ms: 604800000
    segment.bytes: 1073741824
```

## Crie um produtor 
```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --producer-property 'security.protocol=SSL' \
 --producer-property ssl.truststore.password=password \
 --producer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --topic consumer-group-lab 
```

## Crie 2 consumers e veja que as mesmas mensagens chegam em ambos consumidores

```
 $KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --consumer-property 'security.protocol=SSL' \
 --consumer-property ssl.truststore.password=password \
 --consumer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --property "print.key=true" --property "key.separator=," \
 --topic consumer-group-lab 
 
 ```

Ao enviar as mensagens, os dois consumidores recebem a mesma mensagem.
Isso ocorre por foi criado dois grupos automaticamente.

## Listar grupos

Arquivo command.properties

```
security.protocol=SSL
ssl.truststore.password=password
ssl.truststore.location=/tmp/kafka/truststore.jks
```

```
 $KAFKA_HOME/bin/kafka-consumer-groups.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --command-config /tmp/kafka/command.properties \
 --describe --all-groups
```

Consultar número de partições

```
$KAFKA_HOME/bin/kafka-topics.sh  \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --command-config /tmp/kafka/command.properties \
 --describe \
 --topic consumer-group-lab 
```

## Criar grupos 

Consumidor grupo A com key e value

```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443  \
--consumer.config /tmp/kafka/command.properties  \
--topic consumer-group-lab  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```

Produtor simplificado com  key e value

```
$KAFKA_HOME/bin/kafka-console-producer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443  \
--producer.config /tmp/kafka/command.properties  \
--property "parse.key=true" --property "key.separator=," \
--topic consumer-group-lab 
```

Agora cada consumidor esta conectado a uma partição e recebendo mensagens destas partições somente.

## Criar um terceiro consumidor para o grupo-a

```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443  \
--consumer.config /tmp/kafka/command.properties  \
--topic consumer-group-lab  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```
Como so existem 2 partições este consumidor ficará em espera ate ter uma partição disponível.
