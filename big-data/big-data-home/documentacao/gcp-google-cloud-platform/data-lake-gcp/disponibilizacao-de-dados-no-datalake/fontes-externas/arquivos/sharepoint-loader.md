
[Documentação](../../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../../data-lake-gcp.md) > [Disponibilizacao de dados no Datalake](../../../disponibilizacao-de-dados-no-datalake.md) > [Fontes externas](../../fontes-externas.md) > [Arquivos](../arquivos.md)

# Sharepoint Loader

Componente criado para possibilitar a carga de arquivos do sharepoint para tabelas nativas do BigQuery, dentro da estrutura do Datalake.

- [Funcionamento](#funcionamento)
- [Acessos necessários](#acessos-necess-rios)
- [Arquitetura - Visão geral](#arquitetura-vis-o-geral)
- [Limitações](#limita-es)
- [Exceções destacadas](#exce-es-destacadas)

# Funcionamento

1. Preencher e versionar um arquivo de parâmetros, explicitando qual tipo de dado será enviado para o datalake. No próprio repositório tem um modelo a ser seguido.

1. 1. Template do arquivo de parâmetros: [Template](https://stash.uol.intranet/projects/BIBD/repos/app-caribe-schema-files/browse/README.md).
2. A função sharepoint-loader será executada automaticamente a cada 1 hora. Já existe uma DAG programada para isso, não sendo necessário interagir com o airflow.
3. Colocar o arquivo no Sharepoint do Datalake, manualmente, ou via automação.
   1. Serão carregados no Datalake somente arquivos que estiverem no [Sharepoint Datalake](https://uolinc.sharepoint.com/sites/UOLCSDatalakeFiles/)

# Acessos necessários

- [Repositório](https://stash.uol.intranet/projects/BIBD/repos/app-caribe-schema-files/browse/README.md) onde deve-se versionar parâmetros relevantes para a carga;
- Sharepoint Datalake, na pasta do domínio específico em que é necessário carregar dados (solicitar ao time Caribe)
- [Airflow](https://airflow.data.intranet:8080/home) caso seja necessário iniciar uma extração manualmente. Utilizar a dag [**app\_caribe\_sharepoint\_loader**](https://airflow.data.intranet:8080/tree?dag_id=app_caribe_sharepoint_loader).
- Dataset do domínio específico onde os dados de arquivo serão carregados. (solicitar ao time Caribe)
  - todos os arquivos de um determinado domínio ficarão dentro do mesmo dataset seguinte o padrão: *<dominio>\_files\_raw.*

# Arquitetura - Visão geral

![](../../../../../../../attachments/487528753.png)

# Limitações

- Somente serão carregados arquivos CSV, XLSX ou JSON.

# Exceções destacadas

1. **Arquivos do domínio Publicidade. (****Implementado em 18-03-2024)**

- - **Cenário**:
    - O bucket uolcs-datalake-publicidade-prd não está no projeto uolcs-datalake-prd.
  - **Ajustes feitos no fluxo:**
    - Permissão para a função sharepoint-loader escrever no bucket;
    - Permissão para a função file-loader ler dados do bucket;
    - Criação de um sink no projeto uolcs-datalake-publicidade-prd para enviar os logs de escrita no bucket para o pub/sub da função file-loader
    - Permissão para a service account do sink escrever no tópico da função file-loader no pub/sub

**Documentação complementar:**

[Apresentação do componente](https://uolinc.sharepoint.com/:p:/r/sites/SquadCaribe/Documentos%20Compartilhados/General/Apresenta%C3%A7%C3%B5es/Ingest%C3%A3o%20de%20Arquivos%20Sharepoint.pptx?d=w2b306ce6f4854a648f3b640f776e072c&csf=1&web=1&e=kjMIbd)
