
[Documentação](../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../data-lake-gcp.md) > [Transformacao de dados no Datalake](../transformacao-de-dados-no-datalake.md)

# Exportacao automatica de tabelas para arquivo

Essa configuração permite a exportação de tabelas do Bigquery para um bucket do Google Cloud Storage.  
  
A configuração da exportação é feita através do arquivo queries.yml, onde cada tabela a ser exportada é especificada juntamente com suas configurações de exportação (**na tag export**). As configurações incluem o destino (o local no Google Cloud Storage onde os dados serão armazenados), o formato dos dados (como CSV, JSON, AVRO ou PARQUET), se os dados devem ser comprimidos e, se sim, qual tipo de compressão deve ser usado, o delimitador a ser usado, se uma linha de cabeçalho deve ser incluída, e se os dados contêm informações pessoalmente identificáveis (PII).

#### Explicação dos campos

- **`destination`** : Este é um campo obrigatório. Ele especifica o bucket do Google Cloud Storage onde os dados exportados serão armazenados. Você também pode usar um formato de data personalizado na string de destino usando a função `$date()` . Por exemplo, `gs://bucket/path/$date(%Y-%m-%d)_filename.csv` substituiria `$date(%Y-%m-%d)` pela data atual no formato `YYYY-MM-DD, levando em consideração o valor informado no campo relative_date`.
  - > **Atualmente é necessário solicitar para o time de infraestrutura a criação do bucket no GCP e os usuários que terão acesso ao bucket.**
- **`format`** : Este campo especifica o formato dos dados exportados. Os formatos suportados são `CSV` , `NEWLINE_DELIMITED_JSON` , `AVRO` e `PARQUET` .

- **`compression`** : Este campo opcional específica o tipo de compressão para os dados exportados. Os tipos de compressão suportados dependem do formato escolhido. Para `CSV` e `NEWLINE_DELIMITED_JSON` , o tipo de compressão suportado é `GZIP` . Para `AVRO` , os tipos de compressão suportados são `DEFLATE` e `SNAPPY` . Para `PARQUET` , os tipos de compressão suportados são `SNAPPY` , `GZIP` e `ZSTD` .

- **`delimiter`** : Este campo opcional específica o delimitador usado nos dados exportados. Ele é suportado apenas quando o formato é `CSV` .

- **`header`** : Este campo opcional específica se deve incluir uma linha de cabeçalho nos dados exportados. Ele é suportado apenas quando o formato é `CSV` .

- **`has_pii`** : Este campo opcional específica se os dados exportados contêm informações pessoalmente identificáveis (PII). Se `has_pii` for definido como `true` , você deve fornecer um `permission_id` . Isso é para garantir que as informações pessoalmente identificáveis (PII) sejam tratadas corretamente.

- **`permission_id`** : Este campo opcional é necessário se `has_pii` for definido como `true` . Ele especifica o ID da permissão que será usada para acessar os dados exportados. Isso é para garantir que as informações pessoalmente identificáveis (PII) sejam tratadas corretamente.

**O tamanho da tabela não deve exceder 1GB**. Se exceder, uma exceção será levantada.

Além disso, é importante mencionar que, quando você está criando uma tabela para exportação, **o nome da tabela deve começar com o prefixo 'export\_'**. Isso é uma convenção que ajuda a identificar facilmente as tabelas destinadas à exportação.

Vamos considerar que você tem uma tabela chamada `line_items_info` no seu arquivo `queries.yml` . Aqui está um exemplo de como você pode configurar a tag `export` para essa tabela:

**Exemplo de código com export**

```yml
- name: export_line_items_info
  dataset: relatorios_publicidade
  config:
    materialized: table
    incremental_strategy: insert_overwrite
  export:
    destination: gs://uolcs-datalake-export-publicidade-prd/relatorios/line_items/$date(%Y-%m-%d)_line_items_info.csv
    format: CSV
    header: true
    delimiter: ","
    has_pii: false
  relative_date: today
  query_file: export_line_items_info.sql
```

Neste exemplo, a tag export está configurada para exportar os dados da tabela export\_line\_items\_info para um bucket do Google Cloud Storage. O formato dos dados exportados é CSV, e a primeira linha do arquivo CSV conterá os nomes das colunas (devido à opção header: true). O delimitador usado é a vírgula (,) e ela não possuí pii.
