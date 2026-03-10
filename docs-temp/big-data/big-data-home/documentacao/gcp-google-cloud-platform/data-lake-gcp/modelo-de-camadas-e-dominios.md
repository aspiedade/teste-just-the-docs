
[Documentação](../../../documentacao.md) > [GCP - Google Cloud Platform](../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../data-lake-gcp.md)

# Modelo de camadas e dominios

- [Status](#status)
- [Resumo](#resumo)
- [Definições](#defini-es)
- [Camadas](#camadas)
  - [1. Ingestion](#key-1-ingestion)
  - [2. Raw](#key-2-raw)
  - [3. Curated](#key-3-curated)
  - [4. Datamart](#key-4-datamart)
- [Domínios](#dom-nios)
- [Referências](#refer-ncias)

---

# **Status**

2023-01-24: [Aceito (AP11CAR-199)](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-199?focusedCommentId=3265719&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-3265719)

# **Resumo**

Proposta de separação dos dados em camadas. Usado como referência a [proposta de estrutura do **dbt**.](https://docs.getdbt.com/guides/best-practices/how-we-structure/1-guide-overview)

- **Zona RAW**
  1. **ingestion**: dados replicados da origem, pode conter duplicidade. Visível apenas para as aplicações que fazem as ingestões.
  2. **raw**: dados replicados da origem, sem duplicidade.
- **Zona Curated**
  1. **curated**: junção de dados **raw** para formar **entidades** de negócio.
  2. **datamart**: dados agregados, prontos para uso em relatórios

---

# **Definições**

**Zona:** conceito do Dataplex para agrupar datasets.

**Tipo de zona:** Dataplex suporta somente dois tipos: **raw** e **curated**.

**Dataset**: Conjunto de tabelas no BQ

**Camada**: Separação lógica dos dados por nível de tratamento

---

# **Camadas**

## 1. Ingestion

Usado pelas aplicações de ingestão para escrever os dados replicados da origem. Pode conter duplicidades e colunas adicionadas durante a ingestão.

**Características:**

- ✅ Colunas e tags específicas da ingestão (dat\_ingestion, max\_ref\_date, etc)
- ✅ Dados duplicados
- ✅ Colunas sem tratamento de tipo
- ❌ Usado por outras aplicações

## 2. Raw

Camada que deve ser o espelho dos dados da origem.

**Características:**

- ✅ Replica da origem
- ✅ Gerado automaticamente
- ✅ Usado para finalidades específicas (ciência de dados, outras aplicações, etc)
- ❌ Disponível para áreas de negócio
- ❌ Joins

## 3. Curated

Mapeamento de entidades de negócio. Primeira camada disponível para áreas de negócio.

**Características:**

- ✅ Nome por entidade
- ✅ Menor granularidade possível
- ✅ Wide and denormalized
- ✅ Acessível para todos
- ❌ Mesma entidade com regras diferentes para times diferentes
- ❌ Muitos JOINs para única entidade
- ❌ Agregações
- ❌ Métricas

## 4. Datamart

Camada de ativação. Dados prontos para serem consumidos e com valor agregado.

**Características:**

- ✅ Métricas
- ✅ Separado por domínio ou área de interesse
- ✅ Acessível para todos
- ✅ PowerBI
- ❌ Usa dados diretamente da raw

---

# **Domínios**

**Valores válidos para domínios ao inserir ou transformar dados no DataLake**

| Domínio                 | Descrição                                                                   |
|:------------------------|:----------------------------------------------------------------------------|
| **analytics**           | Times de Analytics                                                          |
| **base**                | Dados que são úteis várias áreas                                            |
| **billing**             | Dados de cobrança (UOL7, obusta)                                            |
| **cadastro**            | Dados cadastrais extraídos da plataforma                                    |
| **conteudo**            | Área de conteúdo                                                            |
| **govdados**            | D&A Governança de dados                                                     |
| **folha**               | Folha SP                                                                    |
| **ingresso**            | Ingresso                                                                    |
| **host**                | UOL Host                                                                    |
| **pd**                  | Produtos Digitais                                                           |
| **pdhost**              | Dados do contexto Produtos Digitais + Host, por exemplo custos de marketing |
| **platcorp\_seguranca** | Platcorp Segurança                                                          |
| **publicidade**         | Área de publicidade, por exemplo Uol Ads                                    |

Para permissão de acesso aos dados do domínio, consulte a relação de Data Stewards:

- <https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Data-Steward.aspx>

---

# **Referências**

- How we structure our dbt projects:
  - <https://docs.getdbt.com/guides/best-practices/how-we-structure/1-guide-overview>
  - <https://docs.getdbt.com/guides/best-practices/how-we-structure/2-staging>
  - <https://docs.getdbt.com/guides/best-practices/how-we-structure/3-intermediate>
  - <https://docs.getdbt.com/guides/best-practices/how-we-structure/4-marts>
