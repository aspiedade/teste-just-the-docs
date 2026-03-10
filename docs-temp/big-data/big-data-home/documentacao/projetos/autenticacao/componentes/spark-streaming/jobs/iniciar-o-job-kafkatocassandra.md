
[Documentação](../../../../../../documentacao.md) > [Projetos](../../../../../projetos.md) > [Autenticacao](../../../../autenticacao.md) > [Componentes](../../../componentes.md) > [Spark Streaming](../../spark-streaming.md) > [Jobs](../jobs.md)

# Iniciar o job KafkaToCassandra

```java
hadoop fs -rm /user/svcacc_sparkjobs/shutdown/job/KafkaToCassandra/file

/bin/spark-submit --master yarn --deploy-mode cluster \
--packages datastax:spark-cassandra-connector:2.4.0-s_2.11 \
--jars /home/svcacc_sparkjobs/commons-configuration-1.10.jar \
--driver-memory 1g   --num-executors 9 --executor-cores 1 --executor-memory 1g    \
--class br.com.uol.bigdata.spark.KafkaToCassandra     \
--conf spark.streaming.receiver.writeAheadLog.enable=false    \
--conf spark.streaming.backpressure.enabled=false \
--conf spark.streaming.kafka.maxRatePerPartition=1000 \
--conf spark.eventLog.compress=true \
--conf spark.cassandra.connection.host=10.80.0.18,10.80.0.14,10.80.0.24 \
--conf spark.cassandra.connection.port=7183 \
 /home/svcacc_sparkjobs/authentication-stream.jar \
 "kafka-node3-dev:9092" \
 "gaudi_authentication" \
 "group_authentication_consumer_cassandra_new8" \
 "AuthenticationConsumerCassandra" \
  10000 \
 "latest" \
 "hdfs:///user/svcacc_sparkjobs/shutdown/job/KafkaToCassandra/file"
```

Este job tem a capacidade de processar 240000 mensagens em ~8 segundos.
