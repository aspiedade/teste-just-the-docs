
[Documentação](../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../data-lake-gcp.md) > [Disponibilizacao de dados no Datalake](../../disponibilizacao-de-dados-no-datalake.md) > [Fontes externas](../fontes-externas.md)

# Dag Maker

A ingestão de dados é feita em cargas batch utilizando o Cloud Run + Airflow.

Cadastro de ingestão através do repositório: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch-pipelines/browse>

# **Visão geral**

# **Bases suportadas**

| Base       | Versões testadas   | Carga full   | Incremental por data   | Incremental por ID   |
|:-----------|:-------------------|:-------------|:-----------------------|:---------------------|
| Oracle     |                    | **✅**       | **✅**                 | **✅**               |
| SQL Server |                    | **✅**       | **✅**                 | **✅**               |
| MySQL      |                    | **✅**       | **✅**                 | **✅**               |
| PostgreSQL |                    | **✅**       | **✅**                 | **✅**               |
| MongoDB    |                    | **✅**       | **✅**                 | **❌**               |

# **Limitações**

- Hoje a ingestão é feita via Cloud Run que tem timeout de 1 hora, por conta disso o número de registros carregados por execução é limitado
- Em cargas incrementais, como trazemos o que foi alterado baseado em uma coluna de gatilho, caso tenha sido removidos dados na origem, não conseguimos saber quais foram os registros removidos para replicar no DataLake, Somente se for realizada uma carga full;
  - Nesse caso uma alternativa é a tabela da origem fazer somente uma deleção lógica: marcar a linha como deletada, mas manter na tabela;
