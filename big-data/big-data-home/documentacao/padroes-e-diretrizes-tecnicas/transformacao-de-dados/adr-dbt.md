
[Documentação](../../../documentacao.md) > [Padroes e Diretrizes Tecnicas](../../padroes-e-diretrizes-tecnicas.md) > [Transformacao de Dados](../transformacao-de-dados.md)

# ADR - dbt

- [Status](#status)
- [Contexto](#contexto)
  - [Limitações Dataform (até a data da escolha):](#limita-es-dataform-at-a-data-da-escolha)
  - [Limitações em comum de ambas](#limita-es-em-comum-de-ambas)
- [Escolha do dbt](#escolha-do-dbt)

# **Status**

**2022-12**: Aceito. Detalhes:

1. [AP11CAR-199 Nova arquitetura no GCP](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-199 "Nova arquitetura no GCP")

# **Contexto**

Após decisão de construção do Data Lake baseado em BigQuery, foi necessário escolher uma ferramenta principal para controle das transformações de dados.

Dentre as soluções disponíveis, optamos por estudas duas principais: Dataform e dbt.

## **Limitações Dataform (até a data da escolha):**

- Não controla as partições em transformações incrementais automaticamente;
- Ainda estava em "preview", não havendo garantias que se tornaria GA;
- Apresentava instabilidades na aplicação de policy tags: não conseguimos material de suporte;

## **Limitações em comum de ambas**

- Precisar de um executor externo, ambos precisam ser invocados via Airflow. Somente dbt Cloud possui integrado na solução;
- Deploy dos artefatos:
  - Dataform: Precisaria de um processo para replicar o repositório do BitBucket para o interno no GCP
  - dbt: Artefatos ficaram no Cloud Run que executa, deploy via Jenkins

# **Escolha do dbt**

**Algumas features que ajudaram na decisão:**

- Facilidade em controlar partições nas transformações
- Usa templates Jinja e não formato próprio
- Suporte a modelos Python que integra com Dataproc
- Mais consolidado no mercado
- Comunidade maior e mais ativa
