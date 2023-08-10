

# Preparar ambiente 

**Preparar ambiente do RHEL**

Caso o ambiente ja tenha sido utilizado será necessário apagar dados antigos

```
rm -rfv /tmp/kafka-logs
```
```
rm -rfv /tmp/zookeeper
```

```
rm -rfv /opt/kafka/kafka_2.13-3.3.1.redhat-00008
```

LAB 1 - Instalação

- [RHEL ](./lab1-install-kafka-rhel.md)
- [Openshift 4 - Instalar Operator ](./lab-install-operator.md)
- [Openshift 4 - Provisionar cluster Kafka ](./lab1-install-kafka-ocp4.md)

LAB 2 
- [Zookeeper ](./lab2-zookeeper.md)

LAB 3
- [Consumer Groups ](./lab3-consumer-group.md)

LAB 4
- [Replication Factory](./lab-replicas.md)


LAB Autenticacao no Openshift 
- [Autenticação no Openshift ](./lab-autenticacao.md)

LAB Kafka Bridge 
- [Kafka Bridge ](./lab-kafka-bridge.md)


LAB Mutual TLS Openshift
- [Mutual TLS Openshift ](./lab-mTls-Openshift.md)


## Refs
https://www.kai-waehner.de/blog/2022/05/12/comparison-jms-api-message-broker-mq-vs-apache-kafka/