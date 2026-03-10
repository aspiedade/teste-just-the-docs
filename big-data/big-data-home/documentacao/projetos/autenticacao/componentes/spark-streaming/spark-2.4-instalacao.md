
[Documentação](../../../../../documentacao.md) > [Projetos](../../../../projetos.md) > [Autenticacao](../../../autenticacao.md) > [Componentes](../../componentes.md) > [Spark Streaming](../spark-streaming.md)

# Spark 2.4 - instalacao

```bash
 wget https://archive.apache.org/dist/spark/spark-2.4.0/spark-2.4.0-bin-hadoop2.6.tgz
 tar xvf spark-2.4.0-bin-hadoop2.6.tgz
 rm spark-2.4.0-bin-hadoop2.6.tgz
 
 cd spark-2.4.0-bin-hadoop2.6/conf/
 
 cp spark-defaults.conf.template /opt/spark-2.4.0-bin-hadoop2.6/conf/
 cp  spark-env.sh.template /opt/spark-2.4.0-bin-hadoop2.6/conf/

--logs do spark
mkdir -p /log1/spark_2.4/spark-events
chmod 777  /log1/spark_2.4/spark-events


--Yarn
 sudo su
 su hdfs


 hadoop fs -mkdir -p hdfs://master-node1-dev/data1/yarn/nm/usercache
 hadoop fs -chown -R yarn:yarn hdfs://master-node1-dev/data1/yarn
 hadoop fs -chmod 777 hdfs://master-node1-dev/data1/yarn/nm/usercache

 
 hadoop fs -mkdir -p hdfs://master-node1-dev/data2/yarn/nm/usercache
 hadoop fs -chown -R yarn:yarn hdfs://master-node1-dev/data2/yarn
 hadoop fs -chmod 777 hdfs://master-node1-dev/data2/yarn/nm/usercache
 
 
 hadoop fs -mkdir -p hdfs://master-node1-dev/data3/yarn/nm/usercache
 hadoop fs -chown -R yarn:yarn hdfs://master-node1-dev/data3/yarn
 hadoop fs -chmod 777 hdfs://master-node1-dev/data3/yarn/nm/usercache
 
 hadoop fs -mkdir -p hdfs://master-node1-dev/data4/yarn/nm/usercache
 hadoop fs -chown -R yarn:yarn hdfs://master-node1-dev/data4/yarn
 hadoop fs -chmod 777 hdfs://master-node1-dev/data4/yarn/nm/usercache


```
