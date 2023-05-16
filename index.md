

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

LAB1 - Instalação

- [RHEL ](./lab1-install-kafka-rhel.md)
- [Openshift 4](./lab1-install-kafka-ocp4.md)

LAB2 
- [Zookeeper ](./lab2-zookeeper.md)


LAB Kafka Bridge 
- [Kafka BRidge ](./lab-kafka-bridge.md)


## Refs
https://www.kai-waehner.de/blog/2022/05/12/comparison-jms-api-message-broker-mq-vs-apache-kafka/