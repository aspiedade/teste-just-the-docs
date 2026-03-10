
[Documentação](../../../../../../documentacao.md) > [Projetos](../../../../../projetos.md) > [Autenticacao](../../../../autenticacao.md) > [Componentes](../../../componentes.md) > [Spark Streaming](../../spark-streaming.md) > [Jobs](../jobs.md)

# Iniciar o job AuthenticationConsumer

<https://stash.uol.intranet/projects/BIBD/repos/authentication-stream/browse/src/main/scala/br/com/uol/bigdata/spark/streaming/AuthenticationConsumer.scala>

Na utility-node, execute os seguintes comandos:

```bash
sudo su


su svcacc_sparkjobs


```

**PRD**

```bash
#exclui o dir de checkpoint
#hadoop fs -rmr /user/svcacc_sparkjobs/checkpoint/authentication_consumer_analytics_prd/

#exclui o arquivo de shutdown
hadoop fs -rm /user/svcacc_sparkjobs/shutdown/job/authentication_consumer_analytics_prd/file


#executa o job spark em modo cluster
/bin/spark-submit --master yarn --deploy-mode cluster    \
 --driver-memory 1g   --num-executors 5 --executor-cores 2 --executor-memory 500mb     \
 --class br.com.uol.bigdata.spark.streaming.AuthenticationConsumer  \
 --conf spark.streaming.receiver.writeAheadLog.enable=false  \
 --conf spark.yarn.maxAppAttempts=4   \
 --conf spark.yarn.am.attemptFailuresValidityInterval=1h  \
 --conf spark.yarn.max.executor.failures=$((8 * 4))  \
 --conf spark.yarn.executor.failuresValidityInterval=1h  \
 --conf spark.streaming.backpressure.enabled=false \
 --conf spark.streaming.kafka.maxRatePerPartition=4000  \
 --conf spark.eventLog.compress=true \
 /home/svcacc_sparkjobs/spark_jobs/lib/authentication-stream.jar  \
 "kafka-node1-prd:9092,kafka-node2-prd:9092,kafka-node3-prd:9092"  \
 "gaudi_authentication"  \
 "group_authentication_consumer_bigdata"   \
 "AuthenticationConsumerNew"  \
 "master-node1-prd:7051,master-node2-prd:7051,master-node3-prd:7051"  \
 "impala::auth_adm.raw_authentication" \
 "hdfs:///user/svcacc_sparkjobs/shutdown/job/authentication_consumer_analytics_prd/file"  \
 5000  \
 "latest"

```

**QA**

```bash
hadoop fs -rm /user/svcacc_sparkjobs/shutdown/job/authentication_consumer_analytics_prd/file


#executa o job spark em modo cluster
/bin/spark-submit --master yarn --deploy-mode cluster    \
 --driver-memory 1g   --num-executors 4 --executor-cores 2 --executor-memory 1g     \
 --class br.com.uol.bigdata.spark.streaming.AuthenticationConsumer  \
 --conf spark.streaming.receiver.writeAheadLog.enable=false  \
 --conf spark.yarn.maxAppAttempts=4   \
 --conf spark.yarn.am.attemptFailuresValidityInterval=1h  \
 --conf spark.yarn.max.executor.failures=$((8 * 4))  \
 --conf spark.yarn.executor.failuresValidityInterval=1h  \
 --conf spark.streaming.backpressure.enabled=false \
 --conf spark.streaming.kafka.maxRatePerPartition=4000  \
 --conf spark.eventLog.compress=true \
 /home/svcacc_sparkjobs/spark_jobs/lib/authentication-stream.jar  \
 "kafka-node3-dev:9092"  \
 "gaudi_authentication"  \
 "group_authentication_consumer_bigdata"   \
 "AuthenticationConsumerNew"  \
 "master-node1-dev:7051"  \
 "impala::auth_adm.raw_authentication" \
 "hdfs:///user/svcacc_sparkjobs/shutdown/job/authentication_consumer_analytics_prd/file"  \
 5000  \
 "latest"









```
