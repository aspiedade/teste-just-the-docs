
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Airflow - Verificar DAGs e Tasks em execucao

# DAGs em status running

**Browser > DAG Runs > Search >  State startswith "run"**

<https://airflow.data.intranet:8080/dagrun/list/?_flt_0_state=run>

## Tasks ativas

Uma forma: olhando no pool os slots em uso

**Admin > Pools > Running Slots**

<https://airflow.data.intranet:8080/taskinstance/list/?_flt_3_pool=default_pool&_flt_3_state=running>
