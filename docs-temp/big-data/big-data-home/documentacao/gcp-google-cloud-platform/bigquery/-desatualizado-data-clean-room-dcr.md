
[Documentação](../../../documentacao.md) > [GCP - Google Cloud Platform](../../gcp-google-cloud-platform.md) > [BigQuery](../bigquery.md)

# [Desatualizado] Data Clean Room - DCR

- [Premissas](#premissas)
- [Preparando dados](#preparando-dados)
- [Privacy Policies](#privacy-policies)
- [Analytics Hub - Criando um ambiente DCR](#analytics-hub-criando-um-ambiente-dcr)
- [Consumindo dados](#consumindo-dados)
- [Limitações](#limita-es)
- [Documentação](#documenta-o)

DCRs são ambientes seguros para compartilhar, mesclar e analisar dados sensíveis.

## Premissas

- Possuir um projeto no GCP
- Os dados devem estar em views/tabelas nos datasets do Bigquery

## Preparando dados

Os publicadores de dados não querem que as pessoas visualizem e copiem dados brutos(raw) disponíveis em uma DCR. Por isso é importante adicionar [Privacy Policies](https://cloud.google.com/bigquery/docs/privacy-policies) em todas as Authorized views disponíveis no DCR. Tabelas não são aceitas.

1. Criar um **dataset** para ser adicionado ao DCR
2. Criar uma **[Authorized View](https://cloud.google.com/bigquery/docs/share-access-views)** com os dados que gostaria de adicionar
3. Aplicar **[Privacy Policies](https://cloud.google.com/bigquery/docs/privacy-policies)** para cada Authorized view no seu dataset

## Privacy Policies

Como publicador de dados, você pode configurar *políticas de privacidade* nas views compartilhadas na DCR. As [políticas de privacidade](https://cloud.google.com/bigquery/docs/privacy-policies) impedem o acesso bruto aos dados e aplicam restrições de consulta, e permitem aos consumidores analisarem dados somente por meio de [consultas de agregação](https://cloud.google.com/bigquery/docs/privacy-policies#agg_threshold_policy_functions).

```sql
CREATE OR REPLACE VIEW mydataset.user_all_view
OPTIONS(
  privacy_policy= '{"aggregation_threshold_policy": {"threshold": 3, "privacy_unit_columns": "idt_person"}}'
)
AS ( SELECT * FROM mydataset.user_all);
```

1. **privacy\_unit\_columns**: A coluna que está sendo protegida na view. É possível selecionar essa coluna somente com funções agrupáveis, ex: **count distinct**.  Utilizada também para mesclar tabelas do DCR com tabelas internas.
2. **aggregation\_threshold\_policy**: Ao realizar uma consulta, se o agrupamento distinto da coluna acima não satisfazer o número desejado, essa linha é omitida do resultado. Ex: Ao selecionar a quantidade de usuários por produto, a consulta somente retornará produtos com mais de 3 usuários.

## Analytics Hub - Criando um ambiente DCR

1. Acesse o [Analytics hub](https://console.cloud.google.com/bigquery/analytics-hub)
2. Crie uma **data clean room**
3. Adicione um listing(dataset), contendo suas Authorized Views
4. Gerencie e autorize Subscribers (consumidores dos dados)

## Consumindo dados

Quando autorizado pelo publicador, ao consumir os dados é essencial o comando **SELECT WITH AGGREGATION\_THRESHOLD,**necessário para rodar queries em views com políticas de privacidade.

```sql
SELECT WITH AGGREGATION_THRESHOLD
  v.product, COUNT(DISTINCT v.idt_person) AS cnt
FROM mydataset.user_all_view v
-- (OPTIONAL) cruzar dados com bases internas
-- INNER JOIN company_dataset.internal_company_table t on t.idt_person = v.idt_person
GROUP BY v.product;
```

[Mais exemplos](https://cloud.google.com/bigquery/docs/privacy-policies#view_in_privacy_query)

## Limitações

## Documentação

1. <https://cloud.google.com/bigquery/docs/data-clean-rooms>
2. <https://cloud.google.com/bigquery/docs/authorized-views>
3. <https://cloud.google.com/bigquery/docs/privacy-policies>
