
[Documentação](../../../../../../documentacao.md) > [Projetos](../../../../../projetos.md) > [Autenticacao](../../../../autenticacao.md) > [Componentes](../../../componentes.md) > [Spark Streaming](../../spark-streaming.md) > [Jobs](../jobs.md)

# Parar o job AuthenticationConsumer

```bash
#stop job spark streaming
sudo su


su svcacc_sparkjobs

hadoop fs -put /home/svcacc_sparkjobs/file  /user/svcacc_sparkjobs/shutdown/job/authentication_consumer_analytics_prd/


```
