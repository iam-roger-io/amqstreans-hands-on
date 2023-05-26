

# mTLS Authentication

# Pre Requisito

Cluster em execução com o listener com tls habilitado. Acesso externo ao OCP
```
- authentication:
    type: tls
name: external
port: 9094
tls: true
type: route
```

Usuario configurado para autenticação TLS

```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaUser
metadata:
  creationTimestamp: '2023-05-26T19:45:31Z'
  generation: 1
  labels:
    strimzi.io/cluster: amqstreams-lab
  name: userm
  namespace: amq-streams-lab

spec:
  authentication:
    type: tls
```

## Com Certificado do usuário criado pelo Strimzi

*Baixar o certificado do cluster*


```
oc extract secret/amqstreams-lab-cluster-ca-cert --keys=ca.crt --to=- > ca.crt
```

*Criar o truststore com o certificado do cluster*

```
keytool -import -trustcacerts -alias root -file ca.crt -keystore cluster-truststore.jks -storepass mypassword -noprompt
```

*Baixar certificado do usuario*
```
oc extract secret/userm  --keys=user.p12 --to=- > user.p12
```

*Criar Keystore com o certificado do usuário*
```
oc extract secret/userm  --keys=user.password --to=- > user.password
```

```
keytool -keystore user-truststore.jks -alias CARoot -import -file ca.crt
```

*Neste comando sera pedido uma senha no prompt. Informe a senha do user.password *

```
keytool -importkeystore -srckeystore user.p12 -srcstoretype pkcs12 -destkeystore user-keystore.jks -deststoretype jks -storepass mypassword2 -noprompt
```

```
 $KAFKA_HOME/bin/kafka-console-producer.sh \
 --bootstrap-server  amqstreams-lab-kafka-bootstrap-amq-streams-lab.apps-crc.testing:443 \
 --producer-property 'security.protocol=SSL'  \
 --producer-property ssl.truststore.password=mypassword  \
 --producer-property ssl.truststore.location=./cluster-truststore.jks \
 --producer-property ssl.keystore.location=./user-keystore.jks \
 --producer-property ssl.keystore.password=mypassword2 \
 --producer-property ssl.key.password=Tz2uzd34dHsiGOohYy54ZzQ2tCM5GAjS \
 --topic my-topic
 ```