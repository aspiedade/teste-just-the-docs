
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# [Python][BigQuery] Sobrescrever particao de tabela

## Exemplo de criação ou sobrescrita de partição em tabela particionada por coluna

- Adicionar "field" no TimePartitioning
- Adicionar partition decorator ao nome da tabela (table$yyyymmdd)

```py
from google.cloud import bigquery

client = bigquery.Client()
job_config = bigquery.LoadJobConfig(
            write_disposition='WRITE_TRUNCATE',
            create_disposition="CREATE_IF_NEEDED",
            time_partitioning=bigquery.TimePartitioning(
                type_=bigquery.TimePartitioningType.DAY,
                field="date"
            )
)
job = client.load_table_from_json(
    [{'date': '2024-01-01', 'col': '456'}],
    'uolcs-caribe-qa.dbt_damiao.teste$20240101',
    job_config=job_config
)
job.result()
```
