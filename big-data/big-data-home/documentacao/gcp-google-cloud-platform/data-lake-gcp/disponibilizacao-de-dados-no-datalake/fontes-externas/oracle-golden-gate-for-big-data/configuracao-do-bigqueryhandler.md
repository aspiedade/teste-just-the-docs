
[Documentação](../../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../../data-lake-gcp.md) > [Disponibilizacao de dados no Datalake](../../../disponibilizacao-de-dados-no-datalake.md) > [Fontes externas](../../fontes-externas.md) > [Oracle Golden Gate for Big Data](../oracle-golden-gate-for-big-data.md)

# Configuracao do BigQueryHandler

## Credenciais

Criar conta de serviço e subir arquivo json para instância

Obs:

O BigQueryHandler não suporta autenticação via ADC (application-default). Ele sempre busca pelo caminho na config **gg.handler.bigquery.credentialsFile** ou a variável de ambiente **GOOGLE\_APPLICATION\_CREDENTIALS**

```bash
# Exemplo de arquivo com credenciais
/etc/gcp/uolcs-caribe-qa-ba3d452f2526.json
```

## Dependências

Baixar dependencias:

```bash
cd /u01/app/oracle/product/oggbig/opt/DependencyDownloader
./bigquery.sh 2.24.5
```

## Config replicat

Exemplo de configuração:

```bash
# Properties file for Replicat BQTEST01
#Google BigQuery Handler Template
gg.handlerlist=bigquery
gg.handler.bigquery.type=bigquery
#TODO: Set the Google project id.
gg.handler.bigquery.projectId=uolcs-caribe-qa
gg.handler.bigquery.credentialsFile=/etc/gcp/uolcs-caribe-qa-ba3d452f2526.json
gg.handler.bigquery.auditLogMode=false
gg.handler.bigquery.enableAlter=false
gg.handler.bigquery.metaColumnsTemplate=${optype},${position}
#TODO: Set the location of the BigQuery client libraries.
gg.classpath=/u01/app/oracle/product/oggbig/opt/DependencyDownloader/dependencies/bigquery_2.7.1/*
#Uncomment and configure to set JVM heap size and proxy settings.
#jvm.bootoptions=-Xmx512m -Xms32m -Dhttps.proxyHost= -Dhttps.propxyPort=
```
