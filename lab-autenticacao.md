#
# Autenticação no Openshift
[Index](./index.md)

Listener para configurar autenticação interna SCARM-512
```
      - authentication:
          type: scram-sha-512
        name: tls
        port: 9095
        type: internal
        tls: true
```

Criar usuário via operator

```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaUser
metadata:
  name: my-user
  labels:
    strimzi.io/cluster: amqstreams-lab
  namespace: amq-streams-lab
spec:
  authentication:
    type: scram-sha-512
```

Consumidor SCRAM-512

```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9095 \
 --producer-property 'security.protocol=SASL_SSL' \
 --producer-property 'sasl.mechanism=SCRAM-SHA-512' \
 --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="my-user" password="SbcQUIrVkK8n";' \
 --producer-property ssl.truststore.password=w6gO8hx0yqDK \
 --producer-property ssl.truststore.location=/opt/kafka/cluster-ca-certs/ca.p12 \
 --topic my-topic
```



[Index](./index.md)