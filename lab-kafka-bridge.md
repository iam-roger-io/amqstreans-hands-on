
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
status:
  conditions:
    - lastTransitionTime: '2023-05-15T21:25:28.242259Z'
      status: 'True'
      type: Ready
  labelSelector: >-
    strimzi.io/cluster=lab-bridge,strimzi.io/name=lab-bridge-bridge,strimzi.io/kind=KafkaBridge
  observedGeneration: 1
  replicas: 1
  url: 'http://lab-bridge-bridge-service.amq-streams-lab.svc:8080'
```

## Enviar uma mensagem

```
curl --location 'https://bridge-amq-streams-lab.apps-crc.testing/topics/my-topic' \
--header 'Content-Type: application/vnd.kafka.json.v2+json' \
--header 'Cookie: c229641c02669af267071efcf325cc98=bf4e4af156e567f5b8673b6c1a30ffbf' \
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


## Pontos fracos
- Não possui um mecanismo proprio de autenticação da API