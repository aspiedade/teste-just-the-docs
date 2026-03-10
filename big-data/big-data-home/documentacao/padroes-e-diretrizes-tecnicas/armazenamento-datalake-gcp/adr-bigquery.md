
[Documentação](../../../documentacao.md) > [Padroes e Diretrizes Tecnicas](../../padroes-e-diretrizes-tecnicas.md) > [Armazenamento Datalake GCP](../armazenamento-datalake-gcp.md)

# ADR - BigQuery

- [Título](#t-tulo)
- [Status](#status)
- [Data](#data)
- [Contexto](#contexto)
- [Decisão](#decis-o)
- [Consequências](#consequ-ncias)
- [Notas:](#notas)

## **Título**

Essa decisão define qual será a principal ferramenta de armazenamento de dados que utilizaremos no Datalake.

## **Status**

Aceito.

Detalhes em:

[AP11CAR-199 Nova arquitetura no GCP](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-199 "Nova arquitetura no GCP")

## **Data**

01/11/2022

## **Contexto**

Necessidade de definir qual seria o meio primário de armazenamento de dados no BigQuery.

Foram levantados os prós e contras de gravar dados em tabelas nativas do BigQuery ou seguir o modelo de dados persistidos em arquivo no GCS.

## **Decisão**

Será utilizado primariamente o armazenamento do próprio BigQuery.

## **Consequências**

**Positiva:**

- O custo por GB do BigQuery é semelhante ao GCS
- Performance melhor do que se o armazenamento for no GCS
- Features de permissionamento e mascaramento de dados.
- Mudança automática no storage de uma tabela com mais de 90 dias sem alterações para o modo "Long-term" que reduz em ~50% o custo por GB

**Negativa:**

- Lock-in na ferramenta

## **Notas:**

- Author: Caribe
- Version: 0.1
- Changelog:

  - 0.1: Versão aceita
