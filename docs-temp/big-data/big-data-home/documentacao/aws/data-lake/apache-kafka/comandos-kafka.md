
[Documentação](../../../../documentacao.md) > [AWS](../../../aws.md) > [Data Lake](../../data-lake.md) > [Apache Kafka](../apache-kafka.md)

# Comandos Kafka

**Tópico**

```bash
#criando um topico cujo nome foi substituido por "${TOPIC}", com 8 partições, que serao replicadas em 2 outras maquinas
kafka-topics --zookeeper {zookeeper-host}:2181 --create --topic ${TOPIC} --partitions 8 --replication-factor 2

#para listar os topicos criados
kafka-topics --zookeeper {zookeeper-host}:2181 --list


#para listar os particionamentos e replicas de um topico:
kafka-topics --zookeeper {zookeeper-host}:2181 --describe --topic ${TOPIC}


#O resultado vai ser algo assim:
Topic:${TOPIC}      PartitionCount:8        ReplicationFactor:2     Configs:
        Topic: ${TOPIC}     Partition: 0    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 1    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 2    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 3    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 4    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 5    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 6    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 7    Leader: 32      Replicas: 32,30 Isr: 32,30

#Onde:
#Leader é o nó responsável pela leitura/gravação da partição. Cada maquina é selecionada aleatoriamente como líder de uma partição
#Replicas é a lista de nós que replicam o log dessa partição, independentemente de serem os líderes ou até mesmo se estão atualmente ativos
#Isr é o conjunto de réplicas "in-sync". Este é o subconjunto da lista de réplicas que está atualmente ativa e acessivel pelo líder


#alterando o tempo de retencao default um topico para 1000 ms (para excluir todas as mensagens do topico)
kafka-topics --zookeeper {zookeeper-host}:2181 --alter --topic ${TOPIC} --config retention.ms=1000

#o comando acima já está deprecated, então se você tiver problemas com ele, utilize o kafka-configs
kafka-configs --zookeeper {zookeeper-host}:2181 --alter --add-config retention.ms=10 --entity-name ${TOPIC} --entity-type topics


#agora ao executar o comando describe, vai aparecer na config o que foi setado como diferente do valor default

kafka-topics --zookeeper {zookeeper-host}:2181 --describe --topic ${TOPIC}
Topic:${TOPIC}      PartitionCount:8        ReplicationFactor:2     Configs:retention.ms=1000
        Topic: ${TOPIC}     Partition: 0    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 1    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 2    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 3    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 4    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 5    Leader: 32      Replicas: 32,30 Isr: 32,30
        Topic: ${TOPIC}     Partition: 6    Leader: 30      Replicas: 30,32 Isr: 32,30
        Topic: ${TOPIC}     Partition: 7    Leader: 32      Replicas: 32,30 Isr: 32,30

#se precisar listar todos os topicos que tiveram a config modificada do padrão, ao inves do nome do topico informe o parametro "topics-with-overrides"
kafka-topics --zookeeper {zookeeper-host}:2181 --describe --topics-with-overrides

Topic:topico_teste1     PartitionCount:3        ReplicationFactor:3     Configs:cleanup.policy=delete
Topic:topico_teste2     PartitionCount:3        ReplicationFactor:3     Configs:retention.ms=1000,cleanup.policy=delete
Topic:topico_teste3     PartitionCount:1        ReplicationFactor:3     Configs:cleanup.policy=compact

#para remover essa config que adicionamos ao topico, utilize o parametro "delete-config" informando a config que deseja excluir
kafka-topics --zookeeper {zookeeper-host}:2181 --alter --topic ${TOPIC} --delete-config retention.ms

#idem do comando para adicionar, se o comando acima não funcionou por deprecated, utilize na forma abaixo: 
kafka-configs --zookeeper {zookeeper-host}:2181 --alter --delete-config retention.ms --entity-name ${TOPIC} --entity-type topics


#Exemplo de partição com tópico com replicação 3, sendo que só tem 2 máquinas kafka no cluster
#o brokerID 32 foi removido do cluster, este broker consta na lista de Replicas mas não está na lista de Isr
Topic:__consumer_offsets        PartitionCount:100      ReplicationFactor:3     Configs:segment.bytes=104857600,cleanup.policy=compact,compression.type=producer
        Topic: __consumer_offsets       Partition: 0    Leader: 30      Replicas: 30,132,32     Isr: 30,132
        Topic: __consumer_offsets       Partition: 1    Leader: 30      Replicas: 32,30,132     Isr: 30,132
        Topic: __consumer_offsets       Partition: 2    Leader: 132     Replicas: 132,32,30     Isr: 132,30
        Topic: __consumer_offsets       Partition: 3    Leader: 30      Replicas: 30,32,132     Isr: 30,132
        Topic: __consumer_offsets       Partition: 4    Leader: 132     Replicas: 32,132,30     Isr: 132,30
(...)
        Topic: __consumer_offsets       Partition: 95   Leader: 132     Replicas: 132,30,32     Isr: 132,30
        Topic: __consumer_offsets       Partition: 96   Leader: 30      Replicas: 30,132,32     Isr: 30,132
        Topic: __consumer_offsets       Partition: 97   Leader: 30      Replicas: 32,30,132     Isr: 30,132
        Topic: __consumer_offsets       Partition: 98   Leader: 132     Replicas: 132,32,30     Isr: 132,30
        Topic: __consumer_offsets       Partition: 99   Leader: 30      Replicas: 30,32,132     Isr: 30,132


```

**Para enviar mensagens a um topico**

```bash
#na hora de escrever uma mensagem no topico, ao inves do zookeeper passamos a lista de hosts (ou o dns no load balancer) do broker (porta 9092) como parametro
kafka-console-producer --broker-list {broker-host}:9092 --topic ${TOPIC}

```

**Listar as mensagens de um tópico**

```bash
#para ler as mensagens do topico, a lista dos hosts deve ser passada como bootstrap-server
#veja que tem o parametro from-beginning, que vai pegar todas as mensagens disponíveis no topico, retirar se não for necessário
kafka-console-consumer --bootstrap-server {broker-host}:9092 --topic ${TOPIC} --from-beginning


```

**Somar a qtde de um tópico**

```java
kafka-run-class kafka.tools.GetOffsetShell --broker-list kafka-node1-dev:9092,kafka-node2-dev:9092 --topic ${TOPIC}  --time -1 | tr ":" " " | awk '{ sum += $3 } END { print sum }'

```

para mais informações, acesse <http://kafka.apache.org/quickstart>
