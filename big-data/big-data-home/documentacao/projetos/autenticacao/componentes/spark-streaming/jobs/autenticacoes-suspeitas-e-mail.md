
[Documentação](../../../../../../documentacao.md) > [Projetos](../../../../../projetos.md) > [Autenticacao](../../../../autenticacao.md) > [Componentes](../../../componentes.md) > [Spark Streaming](../../spark-streaming.md) > [Jobs](../jobs.md)

# Autenticacoes suspeitas E-mail

**Objetivo**

Identificar e enviar a para um tópico Kafka autenticações suspeitas, para que a plataforma tome a melhor decisão sobre o que fazer com o usuário (bloquear, alterar a senha, etc).

**Como**

A identificação das autenticações suspeitas é feita através de um job spark que executa agregações cadastradas num bucket S3(s3a://datalake-artifacts-qa/bigdata/spark/aggregation/suspect\_authentication/sql). A ideia é que o job seja dinâmico, ou seja, caso haja a necessidade de adicionar mais um regra, por exemplo, é só criar um arquivo .sql no diretório do s3 citado anteriormente e criar um novo tópico kafka (se necessário).

**Repositório do job spark**:

<https://stash.uol.intranet/projects/BIBD/repos/authentication-stream/browse/src/main/scala/br/com/uol/bigdata/spark/aggregation/suspect_authentication/SuspectAuthentication.scala>

**Submit do job spark**

- **Parâmetros**:
- namAggregation
- dynamicAllocation
- queryPath
- Kudumasters
- kuduTablesQuery
- kuduTableOutput
- bootstrap-servers
- topicOutput  
    
  **PRD**

  ```bash
  #!/bin/bash
  /bin/spark-submit --master yarn --deploy-mode cluster   \
  --driver-memory 1g   --num-executors 4 --executor-cores 2 --executor-memory 1g     \
    --class br.com.uol.bigdata.spark.aggregation.suspect_authentication.SuspectAuthentication  \
    --conf spark.eventLog.compress=true \
    /home/svcacc_sparkjobs/authentication-stream_test.jar  \
    "AUTH_N_COUNTRIES_SMTP" \
    "true"\
    "s3a://datalake-artifacts-prd/bigdata/spark/aggregation/suspect_authentication/sql/AUTH_N_COUNTRIES_SMTP.sql" \
    "master-node1-prd:7051,master-node2-prd:7051,master-node3-prd:7051" \
    "impala::auth_adm.raw_authentication,impala::auth_adm.suspect_authentication_by_person" \
    "impala::auth_adm.suspect_authentication_by_person" \
    "kafka-node1-prd:9092,kafka-node2-prd:9092,kafka-node3-prd:9092"  \
    "suspect_authentication_vericad"

  ```

  **QA**

  ```bash
  #!/bin/bash
  /bin/spark-submit --master yarn --deploy-mode cluster   \
  --driver-memory 1g   --num-executors 4 --executor-cores 2 --executor-memory 1g     \
    --class br.com.uol.bigdata.spark.aggregation.suspect_authentication.SuspectAuthentication  \
    --conf spark.eventLog.compress=true \
    /home/svcacc_sparkjobs/authentication-stream_test.jar  \
    "AUTH_N_COUNTRIES_SMTP" \
    "true"\
    "s3a://datalake-artifacts-qa/bigdata/spark/aggregation/suspect_authentication/sql/AUTH_N_COUNTRIES_SMTP.sql" \
    "master-node1-dev:7051" \
    "impala::auth_adm.raw_authentication,impala::auth_adm.suspect_authentication_by_person" \
    "impala::auth_adm.suspect_authentication_by_person" \
    "kafka-node3-dev:9092"  \
    "suspect_authentication_vericad"

  ```

**Histórico de execução do job**

```
Tabela auth_adm.spark_aggregation_history, que pode ser acessada via Impala.
```

**Histórico de usuários enviados ao kafka de acordo com cada regra**

```
Tabela auth_adm.suspect_authentication_by_person, que pode ser acessada via Impala.
```

**Agregações em produção**

- [Agregacoes](autenticacoes-suspeitas-e-mail/agregacoes.md)
