#
# Consumer Groups

[Index](./index.md)

## Pré-requisitos

Para lab usando o cluster no OCP, baixar o certificado. 

```
oc project amq-streams-lab
```

```
oc extract secret/amqstreams-lab-cluster-ca-cert --keys=ca.crt --to=- > /tmp/kafka/ca.crt
```

```
keytool -import -trustcacerts -alias root -file /tmp/kafka/ca.crt -keystore /tmp/kafka/truststore.jks -storepass password -noprompt
```

## Crie um tópico com 2 partições (use um dos métodos abaixo)

**Criar através da CLI** (SLIDE)

Criar o arquivo /tmp/kafka/command.properties com os valores abaixo, pois não é possível passálos por linha de comando.

```
security.protocol=SSL
ssl.truststore.password=password
ssl.truststore.location=/tmp/kafka/truststore.jks
```

```
$KAFKA_HOME/bin/kafka-topics.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--command-config /tmp/kafka/command.properties \
--create  \
--topic lab-grupo \
--partitions 2
```

Criar tópico através do CRD (yaml via operator)

```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaTopic
metadata:
  name: lab-grupo
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
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --producer-property 'security.protocol=SSL' \
 --producer-property ssl.truststore.password=password \
 --producer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --topic lab-grupo 
```

## Crie 2 consumers e veja que as mesmas mensagens chegam em ambos consumidores ()

```
 $KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --consumer-property 'security.protocol=SSL' \
 --consumer-property ssl.truststore.password=password \
 --consumer-property ssl.truststore.location=/tmp/kafka/truststore.jks \
 --property "print.key=true" --property "key.separator=," \
 --topic lab-grupo
 
 ```

Ao enviar as mensagens, os dois consumidores recebem a mesma mensagem.
Isso ocorre pois foram criado dois grupos automaticamente.

## Listar grupos

```
  $KAFKA_HOME/bin/kafka-consumer-groups.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --command-config /tmp/kafka/command.properties \
 --describe  \
 --all-groups\
 --describe \
 --members \
 --verbose
```

## Criar grupos 

**Consumidor grupo-a com key e value**

```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--consumer.config /tmp/kafka/command.properties  \
--topic consumer-group-lab  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```

**Produtor simplificado com  key e value**

```
$KAFKA_HOME/bin/kafka-console-producer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--producer.config /tmp/kafka/command.properties  \
--property "parse.key=true" --property "key.separator=," \
--topic lab-grupo
```

Agora cada consumidor esta conectado a uma partição e recebendo mensagens destas partições somente.

## Criar um terceiro consumidor para o grupo-a

```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--consumer.config /tmp/kafka/command.properties  \
--topic lab-grupo  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```

Como so existem 2 partições , 1 consumidor ficará em espera ate ter uma partição disponível.

Até aqui utilizamos a estratégia  Round Robin


#
# Simulando a Estratégia Sticky

- Subir 2 Consumidores
- Envie algumas mensagens

```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--consumer.config /tmp/kafka/command.properties  \
--consumer-property "partition.assignment.strategy=org.apache.kafka.clients.consumer.StickyAssignor" \
--topic lab-grupo  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```

## Consultar grupo específico

```
 $KAFKA_HOME/bin/kafka-consumer-groups.sh --bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --command-config /tmp/kafka/command.properties \
 --describe  \
 --group grupo-a \
 --describe \
 --members \
 --verbose
```

## Consultar número de partições

```
$KAFKA_HOME/bin/kafka-topics.sh  \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --command-config /tmp/kafka/command.properties \
 --describe \
 --topic lab-grupo
```

## Notas


 Exemplo Range Assignment
```
$KAFKA_HOME/bin/kafka-console-consumer.sh \
--bootstrap-server amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--consumer.config /tmp/kafka/command.properties  \
--consumer-property "partition.assignment.strategy=org.apache.kafka.clients.consumer.RangeAssignor" \
--topic lab-grupo  \
--property "print.key=true" --property "key.separator=," \
--group grupo-a
```

Exemplo para trocar o nome do client id

```
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server \
amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443  \
--consumer.config /tmp/kafka/command.properties  \
--consumer-property "client.id=c1" \
--topic lab-grupo  \
--property "print.key=true" \
--property "key.separator=," \
--group grupo-a
```

[Index](./index.md)