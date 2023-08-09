
```
kind: Project
apiVersion: project.openshift.io/v1
metadata:
  name: postgresql-lab
  labels:
    kubernetes.io/metadata.name: postgresql-lab

  annotations:
    openshift.io/description: ''
    openshift.io/display-name: postgresql-lab
spec:
  finalizers:
    - kubernetes
```

```
-- public.student definition

-- Drop table

-- DROP TABLE public.student;

CREATE TABLE public.student (
	id int4 NOT NULL GENERATED ALWAYS AS IDENTITY,
	"name" varchar NULL,
	email varchar NULL
);
```

```
oc port-forward $(oc get pods  -o custom-columns=POD:.metadata.name --no-headers --selector name=postgresql) 5432:5432
```

*Criar um imagestream*

```
kind: ImageStream
apiVersion: image.openshift.io/v1
metadata:
  name: debezium-streams-connect
  namespace: amqstreams-lab
```


```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaConnect
metadata:
  name: debezium-kafka-connect-cluster
  annotations:
    strimzi.io/use-connector-resources: "true" 
spec:
  version: 3.3.1
  build: 
    output: 
      type: imagestream
      image: debezium-streams-connect:latest
    plugins:
      - artifacts:
          - type: zip
            url: >-
              https://maven.repository.redhat.com/ga/io/debezium/debezium-connector-postgres/2.1.4.Final-redhat-00001/debezium-connector-postgres-2.1.4.Final-redhat-00001-plugin.zip
          - type: zip
            url: >-
              https://maven.repository.redhat.com/ga/io/apicurio/apicurio-registry-distro-connect-converter/2.3.3.Final-redhat-00001/apicurio-registry-distro-connect-converter-2.3.3.Final-redhat-00001.zip
          - type: zip
            url: >-
              https://maven.repository.redhat.com/ga/io/debezium/debezium-scripting/2.1.4.Final-redhat-00001/debezium-scripting-2.1.4.Final-redhat-00001.zip
          - type: jar
            url: >-
              https://repo1.maven.org/maven2/org/codehaus/groovy/groovy/3.0.11/groovy-3.0.11.jar
          - type: jar
            url: >-
              https://repo1.maven.org/maven2/org/codehaus/groovy/groovy-jsr223/3.0.11/groovy-jsr223-3.0.11.jar
          - type: jar
            url: >-
              https://repo1.maven.org/maven2/org/codehaus/groovy/groovy-json/3.0.11/groovy-json-3.0.11.jar
        name: debezium-connector-postgres

  bootstrapServers: amqstreams-lab-kafka-bootstrap.amqstreams-lab.svc.cluster.local:9092
  ```


```
apiVersion: kafka.strimzi.io/v1beta2
kind: KafkaConnector
metadata:
  labels:
    strimzi.io/cluster: debezium-kafka-connect-cluster
  name: inventory-connector-postgresql
spec:
  class: io.debezium.connector.postgresql.PostgresConnector
  tasksMax: 1
  config:
    database.hostname: postgresql.postgresql-lab.svc.cluster.local
    database.port: 5432 
    database.user: postgresql
    database.password: postgresql
    database.dbname: labdb
    topic.prefix: student-connector-postgresql 
    table.include.list: public.student  
```

2023-06-18 01:47:36,580 ERROR Postgres server wal_level property must be "logical" but is: replica (io.debezium.connector.postgresql.PostgresConnector) [pool-2-thread-1]
2023-06-18 01:47:36,585 ERROR Postgres roles LOGIN and REPLICATION are not assigned to user: postgresql (io.debezium.connector.postgresql.PostgresConnector) [pool-2-thread-1]
2023-06-18 01:47:36,586 INFO Connection gracefully closed (io.debezium.jdbc.JdbcConnection) 
[pool-4-thread-1]


superuser postgres/postgres

# Referências

    https://access.redhat.com/documentation/en-us/red_hat_integration/2023.q2/html-single/debezium_user_guide/index#using-streams-to-deploy-debezium-postgresql-connectors
