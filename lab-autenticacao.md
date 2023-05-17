#
# Autenticação no Openshift
[Index](./index.md)

**Listener para configurar autenticação interna SCARM-512**

Listener para acesso dentro do Openshift
```
      - authentication:
          type: scram-sha-512
        name: login
        port: 9095
        type: internal
        tls: true
```

Listener para acesso fora do Openshift
```
      - authentication:
          type: scram-sha-512
        name: loginroute
        port: 9096
        tls: true
        type: route
```

**Criar usuário via operator**

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

**Consumidor SCRAM-512**

** Dentro do Openshift (Direto no POD)**
```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-bootstrap.amq-streams-lab.svc.cluster.local:9095 \
 --producer-property 'security.protocol=SASL_SSL' \
 --producer-property 'sasl.mechanism=SCRAM-SHA-512' \
 --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="my-user" password="SbcQUIrVkK8n";' \
 --producer-property ssl.truststore.password=w6gO8hx0yqDK \
 --producer-property ssl.truststore.location=/opt/kafka/cluster-ca-certs/ca.p12 \
 --topic my-topic
```
** Fora do Openshift**

Obter certificado do cluster kafka no Openshift se for utilizar um listener externo.

```
oc project amq-streams-lab
```

```
oc extract secret/amqstreams-lab-cluster-ca-cert --keys=ca.crt --to=- > /opt/kafka/ca.crt
```

```
keytool -import -trustcacerts -alias root -file /opt/kafka/ca.crt -keystore /opt/kafka/truststore.jks -storepass password -noprompt
```

```
$KAFKA_HOME/bin/kafka-console-producer.sh --bootstrap-server amqstreams-lab-kafka-loginroute-bootstrap-amq-streams-lab.apps.cluster-wjkn5.wjkn5.sandbox732.opentlc.com:443 \
 --producer-property 'security.protocol=SASL_SSL' \
 --producer-property 'sasl.mechanism=SCRAM-SHA-512' \
 --producer-property 'sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="my-user" password="SbcQUIrVkK8n";' \
 --producer-property ssl.truststore.password=password \
 --producer-property ssl.truststore.location=/opt/kafka/truststore.jks \
 --topic my-topic 
```

**Secret de usuário**
Utilize essa secret se quiser utilizar a mesma senha do consumidor acima

```
kind: Secret
apiVersion: v1
metadata:
  name: my-user
  namespace: amq-streams-lab
  labels:
    app.kubernetes.io/instance: my-user
    app.kubernetes.io/managed-by: strimzi-user-operator
    app.kubernetes.io/name: strimzi-user-operator
    app.kubernetes.io/part-of: strimzi-my-user
    strimzi.io/cluster: amqstreams-lab
    strimzi.io/kind: KafkaUser
data:
  password: U2JjUVVJclZrSzhu
  sasl.jaas.config: >-
    b3JnLmFwYWNoZS5rYWZrYS5jb21tb24uc2VjdXJpdHkuc2NyYW0uU2NyYW1Mb2dpbk1vZHVsZSByZXF1aXJlZCB1c2VybmFtZT0ibXktdXNlciIgcGFzc3dvcmQ9IlNiY1FVSXJWa0s4biI7
type: Opaque
```
[Index](./index.md)
