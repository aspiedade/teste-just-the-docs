
[Documentação](../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../data-lake-gcp.md) > [Transformacao de dados no Datalake](../transformacao-de-dados-no-datalake.md)

# DBT - Alerta de custo na entrega de queries

Estamos implementando um alerta com a estimativa de custo na entrega de queries via Jenkins no query\_maker.

Caso uma query ultrapasse 5TB de processamento mensal, será exibido um aviso e será necessário o usuário aprovar para continuar o deploy.

O número de 5TB/mês foi definido partir de uma análise de todos os agendamentos, onde verificamos que em maio/25 somente 3% das queries consumiam mais que isso, mas representavam 56% do custo total.

Mais detalhes: [20250528\_estudo\_custo\_queries\_para\_trava.xlsx](https://uolinc.sharepoint.com/:x:/r/sites/SquadCaribe/Documentos%20Compartilhados/General/Documentos/20250528_estudo_custo_queries_para_trava.xlsx?d=we22571c1b67a47e08a7a9bd9898d5e77&csf=1&web=1&e=vfBv4d)  e [AP11CAR-1047](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-1047)

# O que fazer caso receba o alerta?

## **1. Avalie se é possível otimizar a query**

Referências:

- <https://cloud.google.com/bigquery/docs/best-practices-performance-compute>
- [Otimizacao com queries incrementais](otimizacao-com-queries-incrementais.md)

### 1.1. Utilize materialização incremental sempre que possível

1.2. Verifique se as tabelas consumidas pela query possuem Particionamento e Cluster.

1.2.1. Caso não possuam, verifique com o time responsável pela criação ou ingestão a possibilidade de adicionar Partição ou Cluster

## **2. Verifique o intervalo de execução**

Analise a real necessidade e se os dados vão ser utilizados.

Exemplo: Execuções intraday de hora em hora, durante a madrugada precisam ser executadas?

## Caso precise de uma ajuda para entender o custo ou auxilio na otimização, envolva algum time de engenharia:

- Caribe
- Roma
- Chicago
