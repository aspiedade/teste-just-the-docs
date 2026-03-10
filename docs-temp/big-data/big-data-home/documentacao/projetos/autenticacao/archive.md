
[Documentação](../../../documentacao.md) > [Projetos](../../projetos.md) > [Autenticacao](../autenticacao.md)

# Archive

O Job para arquivar as autenticações maiores que um mês é executado na máquina utility-node1-prd, via crontab, às 02h30, todos os dias.

O script shell está localizado no seguinte diretório:

/home/svcacc\_sparkjobs/spark\_jobs/cron/

authentication-archive.sh

```bash
#!/bin/bash

/bin/spark-submit --master yarn --deploy-mode cluster  \
--class br.com.uol.bigdata.spark.archive.AuthenticationArchive  \
--conf spark.sql.parquet.compression.codec=gzip  \
--driver-memory 1g  --num-executors 4 --executor-cores 2 --executor-memory 2g    \
/home/svcacc_sparkjobs/spark_jobs/lib/authentication.jar \
"master-node1-prd:7051,master-node2-prd:7051,master-node3-prd:7051"   \
"worker-node3-prd:21050" \
"dat_partition" \
${senha do usuario spark} \
"prd"
```

```bash
30 02 * * * svcacc_sparkjobs /home/svcacc_sparkjobs/spark_jobs/cron/authentication-archive.sh
```

Git: <https://stash.uol.intranet/projects/BIBD/repos/authentication-stream/browse/src/main/scala/br/com/uol/bigdata/spark/archive/AuthenticationArchive.scala>

Os dados são salvos no seguinte bucket/diretório no s3:

[Amazon S3](https://s3.console.aws.amazon.com/s3/home?region=us-east-1)/[bigdata-archive-prd](https://s3.console.aws.amazon.com/s3/buckets/bigdata-archive-prd/?region=us-east-1&tab=overview)/[cloudera](https://s3.console.aws.amazon.com/s3/buckets/bigdata-archive-prd/cloudera/?region=us-east-1&tab=overview)/[database](https://s3.console.aws.amazon.com/s3/buckets/bigdata-archive-prd/cloudera/database/?region=us-east-1&tab=overview)/[auth\_adm](https://s3.console.aws.amazon.com/s3/buckets/bigdata-archive-prd/cloudera/database/auth_adm/?region=us-east-1&tab=overview)/[raw\_authentication](https://s3.console.aws.amazon.com/s3/)"
