
[Documentação](../../../../../../documentacao.md) > [Projetos](../../../../../projetos.md) > [Autenticacao](../../../../autenticacao.md) > [Componentes](../../../componentes.md) > [Spark Streaming](../../spark-streaming.md) > [Jobs](../jobs.md)

# Deploy do job do de autenticacao

Após subir o jar via WinSCP p/ o dir "/home/centos/", execute os seguintes comandos:

```bash
sudo su 
mv /home/svcacc_sparkjobs/spark_jobs/lib/authentication-stream.jar /home/svcacc_sparkjobs/spark_jobs/lib/authentication-stream_${data do bkp}_bkp.jar
mv /home/centos/authentication-stream.jar /home/svcacc_sparkjobs/spark_jobs/lib
```
