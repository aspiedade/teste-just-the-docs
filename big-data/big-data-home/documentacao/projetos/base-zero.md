
[Documentação](../../documentacao.md) > [Projetos](../projetos.md)

# Base Zero

- [Premissas](#premissas)
  - [Bases](#bases)
  - [Acessos](#acessos)
  - [Ferramentas de exploração](#ferramentas-de-explora-o)
- [Governança](#governan-a)
  - [Catálogo](#cat-logo)
  - [Glossário](#gloss-rio)
- [Ferramentas](#ferramentas)
  - [Airflow](#airflow)
    - [Dados de cadastrais](#dados-de-cadastrais)
    - [DMP](#dmp)
  - [Redshift](#redshift)
    - [Acesso](#acesso)
    - [Scripts](#scripts)
  - [Metabase](#metabase)
    - [Acesso](#acesso)
    - [Collections](#collections)
  - [Glue](#glue)

# Premissas

## Bases

- Cadastral
  - User
  - Inscription
  - Account
- DMP
  - Corporate (usuários cadastrados)
  - Escopo TailTarget

## Acessos

- Todos podem ter acesso
- Não pode conter dados sensíveis

## Ferramentas de exploração

- Visual: Metabase
- SQL: Redshift Spectrum
- Python ou Excel: Usar JDBC do Redshift

# Governança

## Catálogo

Está disponibilizado em tabelas do redshift/athena e pelo metabase:

- Metabase: <https://metabase.intranet:3000/dashboard/70-catalogo>
- Tabelas:
  - schema: **datalake\_catalog**

## Glossário

<https://uolinc.sharepoint.com/:x:/r/sites/SquadCaribe/_layouts/15/Doc.aspx?sourcedoc=%7B01A3E83F-986B-45F1-B1E2-12556BF7181B%7D&file=Gloss%C3%A1rio.xlsx&action=default&mobileredirect=true>

# Ferramentas

## Airflow

### Dados de cadastrais

Os dados vem do Hana e são carregados pelo DagMaker: <https://stash.uol.intranet/projects/BIBD/repos/app-caribe/browse>

### DMP

O time chicago é responsável por enviar os dados exportados DynamoDB para o bucket "**uol-datalake-chicago-raw-prd**" e criação das tabelas externas no Glue

Para a base zero foi necessário realizar um tratamento dos dados em json para uma tabela nativa do redshift:

<https://stash.uol.intranet/projects/CCG/repos/ccg-app-dmp-datalake/browse/airflow%202.0/dag/ccg_uol_dmp_materialized_views.py?at=refs%2Fheads%2Fredshift-materialized-views>

- Todo dia pegamos o último dump do DynamoDB que contém os usuários ativos do DMP dos últimos 90 dias

## Redshift

### Acesso

- Solicitar acesso: [Tutorial conectar ao Redshift](../aws/data-lake/redshift/tutoriais/tutorial-conectar-ao-redshift.md)
- Adicionar ao grupo: **g\_base\_zero\_dev**

### Scripts

- <https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/browse/redshift/datalake/base_zero>

## Metabase

### Acesso

Todos usuários tem acesso

### Collections

- **Base Zero:** <https://metabase.intranet:3000/collection/256-base-zero>

## Glue

Catálogo do Glue é exportado para o S3 pela DAG+Lambda: <https://stash.uol.intranet/projects/BIBD/repos/app-datalake-catalog-colletor/browse>
