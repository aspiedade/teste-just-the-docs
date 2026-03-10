
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Airflow](../../apache-airflow.md) > [Dicas-airflow](../dicas-airflow.md)

# Delivery de dags e plugins

No airflow, para fazer o delivery de um dag ou plugin é necessário copiá-los para a pasta **$AIRFLOW\_HOME/dags** ou **$AIRFLOW\_HOME/plugins,**respectivamente. Para facilitar o processo de delivery, foi desenvolvido uma automação para copiar os scripts de um Bucket S3 para os diretórios citados anteriormente.

- **Plugins**

  Copiar o plugin para o diretório **uol-datalake-artifacts-qa|prd/airflow/plugins**, no S3
- **Dag**

  Copiar o dag para o diretório **uol-datalake-artifacts-qa|prd/airflow/dags**, no S3

Após alguns minutos, o dag/plugin estará disponível no Airflow.
