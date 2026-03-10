
[Documentação](../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../data-lake-gcp.md) > [Transformacao de dados no Datalake](../transformacao-de-dados-no-datalake.md)

# DBT - Guia de atualizacao

Guia rápido para auxiliar nas atualizações de versão do DBT.

# **1. Acompanhar versões**

<https://docs.getdbt.com/docs/dbt-versions/core>

# **2. Utilizar o guia de atualização para versão desejada**

<https://docs.getdbt.com/docs/dbt-versions/core-upgrade>

## **2.1 Conferir se nossas macros continuam compatíveis, principalmente as que sobrescrevemos comportamento padrão do dbt**

Nossas macros: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-transformer-artifacts/browse/dbt-project/macros>

# **3. Atualizar versões**

## **3.1 Rodar dbt compile localmente**

Criar um venv com a versão do dbt desejada e rodar um dbt compile no artifacts, para garantir que não quebrou nada

## **3.1 Atualizar dbt-runner**

- Dockerfile: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch/browse/app-caribe-dbt-runner/Dockerfile>
- requirements.txt: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch/browse/app-caribe-dbt-runner/src/requirements.txt>

## **3.2 Atualizar Jenkinsfile (dag\_maker)**

- query\_maker: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch/browse/app-caribe-batch-dag-maker/query.Jenkinsfile>
- source\_maker: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch/browse/app-caribe-batch-dag-maker/source.Jenkinsfile>

## **3.3 Fazer deploy em QA e rodar modelos de validação**

Temos algumas queries que tentam reproduzir os casos de uso mais comuns, como cargas incrementais e testes. Podemos rodar essa DAG para garantir que não quebrou nenhum comportamento atual.

- <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-transformer/browse/queries/demo/dbt_validation_curated>
