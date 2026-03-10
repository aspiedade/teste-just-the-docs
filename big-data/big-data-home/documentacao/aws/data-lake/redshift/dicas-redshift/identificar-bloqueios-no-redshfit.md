
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Redshift](../../redshift.md) > [Dicas - Redshift](../dicas-redshift.md)

# Identificar bloqueios no Redshfit

Query para identificar bloqueios no Redshfit

```sql
SELECT current_time,   
    c.relname,   
    l.database,   
    l.transaction,   
    l.pid,   
    a.usename,   
    l.mode,   
    l.granted
FROM pg_locks l 
JOIN pg_catalog.pg_class c ON c.oid = l.relation
JOIN pg_catalog.pg_stat_activity a ON a.procpid = l.pid
WHERE l.pid <> pg_backend_pid();
```
