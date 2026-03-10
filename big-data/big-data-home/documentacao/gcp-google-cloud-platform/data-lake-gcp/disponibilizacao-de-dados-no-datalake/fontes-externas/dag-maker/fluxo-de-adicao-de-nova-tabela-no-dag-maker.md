
[Documentação](../../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../../data-lake-gcp.md) > [Disponibilizacao de dados no Datalake](../../../disponibilizacao-de-dados-no-datalake.md) > [Fontes externas](../../fontes-externas.md) > [Dag Maker](../dag-maker.md)

# Fluxo de adicao de nova tabela no Dag Maker

- [1. Criação de usuário no banco de dados](#key-1-cria-o-de-usu-rio-no-banco-de-dados)
- [2. Solicitar GRANT de SELECT nas tabelas para o usuário DATALAKE\_ELTUBR](#key-2-solicitar-grant-de-select-nas-tabelas-para-o-usu-rio-datalake-eltubr)
- [3. Solicitar ACL (se necessário)](#key-3-solicitar-acl-se-necess-rio)
- [4. Cadastrar tabelas no Dag Maker](#key-4-cadastrar-tabelas-no-dag-maker)

- [Erros conhecidos](#erros-conhecidos)
  - [MySQL](#mysql)
    - [mysql.connector.errors.ProgrammingError: 1115 (42000): Unknown character set: 'utf8mb4'](#mysql-connector-errors-programmingerror-1115-42000-unknown-character-set-utf8mb4)

## **1. Criação de usuário no banco de dados**

> [!CAUTION]
> **ATENÇÃO: sempre utilizar o nome de usuário datalake\_eltubr**

- Caso ainda não exista, será necessário pedir a criação de um usuário no banco de dados
- Versionar script no repositório [Scripts Portal BD](https://stash.uol.intranet/projects/BIBD/repos/scripts-portal-db/browse/)
- Abertura de chamado para execução: <https://jira.intranet.uol.com.br/jira/servicedesk/customer/portal/41/create/2018>
- Exemplos de scripts:
  - MySQL: <https://stash.uol.intranet/projects/BIBD/repos/scripts-portal-db/browse/MYSQL/webcard/MYSQL_WEBCARD_ADM_PRD.sql>
  - Oracle: <https://stash.uol.intranet/projects/BIBD/repos/scripts-portal-db/browse/ORACLE/OIZUS/ORDER_SERVICE/SYSTEM/oizus_order_service_system_PRD_001.sql>
  - PostgreSQL: <https://stash.uol.intranet/projects/BIBD/repos/scripts-portal-db/browse/POSTGRES/OSTANA/postgreql_billing_services_adm_001.sql>

- Se for uma base nova, cadastrar as credenciais no Secrets Manager (GCP): solicitar para time Caribe

## **2. Solicitar GRANT de SELECT nas tabelas para o usuário DATALAKE\_ELTUBR**

- Versionar script no repositório [Scripts Portal BD](https://stash.uol.intranet/projects/BIBD/repos/scripts-portal-db/browse/)
- Abrir chamado para execução:  
  - Caso o usuário já exista e seja somente para inclusão de novo acesso o link para abertura é: <https://jira.intranet.uol.com.br/jira/servicedesk/customer/portal/41/create/2019>

## **3. Solicitar ACL**(se necessário)

- Enviar e-mail para a lista <l-adm-bd> justificando a necessidade, conforme exemplo:

  *Caros,*

  *Para seguir com a abertura do chamado para SEC liberar ACL do GCP para o banco precisamos do ok de vocês.*

  *O intuito é fazer carga batch de algumas tabelas desse banco para enriquecer o datalake no GCP e possibilitar cruzamentos com outros dados que já estão lá.*

  ***Origem:** GCP*

  ***Rede QA:** 10.224.80.0/22*

  ***Rede Produção**: 10.224.72.0/21*

  ***Destino***

  ***Servidor: xxxx***  
  ***Banco: xxxx***
- Abrir chamado no [portal de SEC](https://jira.intranet.uol.com.br/jira/servicedesk/customer/portal/33/create/767?q=acl&q_time=1613771214860), anexando o retorno do time de banco. Exemplo de preenchimento do chamado:

  ***Empresa**: UOLCS*

  ***Tipo**: Criação*

  ***Local**: None*

  ***Justificativa**: acesso a base do banco **xxxx**para extração de dados para o datalake no GCP.*

  ***Risco/impacto:** Sem acesso as bases*

  ***Tipo**: Permanente*

  ***Dependência**: Colocar a história do nosso quadro*

  *Preencher IPs de origem e destino.*

## **4. Cadastrar tabelas no [Dag Maker](https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch-pipelines/browse/README.md)**

**Mais detalhes no README.md: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe-batch-pipelines/browse>**

---

# **Erros conhecidos**

## **MySQL**

### **mysql.connector.errors.ProgrammingError: 1115 (42000): Unknown character set: 'utf8mb4'**

**Característica**

Esse erro indica que o MySQL não reconhece o charset utf8mb4. Isso geralmente ocorre quando o servidor MySQL é uma versão antiga (antes do 5.5.3), que não suporta utf8mb4.

**Solução**

Na secret adicione altere o charset para utf8. Ex:

**secret.json**

```js
{
  "driver": "mysqlconnector",
  "username": "",
  "password": "",
  "host": "xxx",
  "port": "3306",
  "database_name": "db",
  "charset": "utf8"
}
```
