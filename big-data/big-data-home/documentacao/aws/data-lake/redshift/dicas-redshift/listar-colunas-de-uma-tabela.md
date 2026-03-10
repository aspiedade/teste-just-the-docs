
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Redshift](../../redshift.md) > [Dicas - Redshift](../dicas-redshift.md)

# Listar colunas de uma tabela

```sql
select ordinal_position as position,
       column_name,
       data_type,
       case when character_maximum_length is not null
            then character_maximum_length
            else numeric_precision end as max_length,
       is_nullable,
       column_default as default_value
from information_schema.columns
where table_name = 'datalake_eltubr_base_contato_bd' -- enter table name here
      -- and table_schema = 'Schema name'
order by ordinal_position;
```
