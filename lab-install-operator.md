O pré requisito para a instalação do Operator é ser **Cluster-admin**.

Crie uma namespace. Sugerimos para o lab o nome: **amq-streams-lab**

```
oc new-project amq-streams-lab
```

No menu esquerdo do Openshift procure a opção *Operator Hub*

![Alt text](./images/operator1.png "a title")

Pesquise a palavra **Kafk** e observe que serão exibidos 2 operators: AMQ Streams e Strimzi. O Strinzi é a versão community do AMQ Streams.

Clique no operator **AMQ Streams** e em Seguida no botão install.

![Alt text](./images/operator2.png "a title")

Para o lab selecione a opção **Installed Namespaces** e selecione a namespace desejada. Para fins de lab podemos  aceitar os valores default para as demais propriedades.

![Alt text](./images/operator3.png "a title")

Aguarde a finalização da instalação:

![Alt text](./images/operator4.png "a title")

![Alt text](./images/operator5.png "a title")

Clique no menu esquerdo, na opção **Installed Operators**. observe que o operator ja está instalado

![Alt text](./images/operator6.png "a title")

Clique no menu esquerdo na Opçao **Workloads** e na sequência em **pods**. Observe que há um pod do operator em execução.

![Alt text](./images/operator7.png "a title")

**Estamos prontos para provisionar um cluster Kafka no Openshift .**

##Considerações

- Não é recomendável instalar o AMQ Streams com o escopo de **All namespaces**, a fim de gerenciar multiplos cluster.
- Não é recomendado a instalar o Operator com a estratégia de atualização automática. Isso pode gerar problemas no cluster causados por alterações feitas no operator.