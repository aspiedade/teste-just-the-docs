
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Airflow](../../apache-airflow.md) > [Dicas-airflow](../dicas-airflow.md)

# Criacao de usuario via linha de comando

```bash
#usuário com role Admin
airflow create_user -r Admin -u bigdata_admin -e bigdata_admin@uolinc.com -f bigdata_admin -l bigdata_admin  -p ${PASSW}
```

```bash
#usuário com role OP
airflow create_user -r Op -u bigdata_dev -e bigdata_dev@uolinc.com -f bigdata_dev -l bigdata_dev  -p ${PASSW}
```

<https://airflow.apache.org/security.html>
