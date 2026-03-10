
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Redshift](../../redshift.md) > [Dicas - Redshift](../dicas-redshift.md)

# Distribuicao dos dados por slice

A consulta abaixo retorna a distribuição dos dados por slice, o que pode ser útil para identificar se a distkey está uniforme:

```sql
select trim(name) as table, slice, sum(num_values) as rows, min(minvalue), max(maxvalue)
from svv_diskusage
where name in ('gaudi_authentication_log_88') 
and col =0
group by name, slice
order by name, slice;
```

Referência:

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/tutorial-tuning-tables-retest.html>
