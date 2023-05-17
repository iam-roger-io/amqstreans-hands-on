#
# Kafka Bridge

[Index](./index.md)

## Criar uma instância do Kafka Bridge.
```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaBridge
metadata:
  name: lab-bridge
  namespace: amq-streams-lab
spec:
  bootstrapServers: 'amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9092'
  http:
    port: 8080
  replicas: 1
```

## Enviar uma mensagem

```
curl --location 'http://bridge-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com/topics/bridge-quickstart-topic' \
--header 'Content-Type: application/vnd.kafka.json.v2+json' \
--header 'Cookie: c229641c02669af267071efcf325cc98=c8ee010926fda9ccaaf5a2b976a7abc2' \
--data '{
    "records": [{
        "key": "lab",
        "value": "teste"
    }]
} 

'
```

## Consumir  através consumidor nativo

```
$KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9092 \
--consumer-property 'security.protocol=PLAINTEXT' \
--topic my-topic --from-beginning
```

## Consumir pelo Kafka Bridge

**Criar o consumer**

```
curl --location 'http://bridge-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com/consumers/bridge-quickstart-consumer-group' \
--header 'Content-Type: application/vnd.kafka.v2+json' \
--header 'Cookie: c229641c02669af267071efcf325cc98=c8ee010926fda9ccaaf5a2b976a7abc2' \
--data '{
    "name": "bridge-quickstart-consumer",
    "auto.offset.reset": "earliest",
    "format": "json",
    "enable.auto.commit": false,
    "fetch.min.bytes": 512,
    "consumer.request.timeout.ms": 30000
  }'
```

**Subscribe Consumer e Topico**
```
  curl --location 'http://bridge-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com/consumers/bridge-quickstart-consumer-group/instances/bridge-quickstart-consumer/subscription' \
--header 'Content-Type: application/vnd.kafka.v2+json' \
--header 'Cookie: c229641c02669af267071efcf325cc98=c8ee010926fda9ccaaf5a2b976a7abc2' \
--data '{
    "topics": [
        "bridge-quickstart-topic"
    ]
}'
```

**Receber a mensagem**
```
curl --location 'http://bridge-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com/consumers/bridge-quickstart-consumer-group/instances/bridge-quickstart-consumer/records' \
--header 'Accept: application/vnd.kafka.json.v2+json' \
--header 'Cookie: c229641c02669af267071efcf325cc98=c8ee010926fda9ccaaf5a2b976a7abc2' \
--data ''
```

## Referências

https://strimzi.io/docs/bridge/latest/

[Index](./index.md)