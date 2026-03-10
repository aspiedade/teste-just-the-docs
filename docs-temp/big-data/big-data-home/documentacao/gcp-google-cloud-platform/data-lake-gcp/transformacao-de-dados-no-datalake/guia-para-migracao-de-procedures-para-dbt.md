
[Documentação](../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../data-lake-gcp.md) > [Transformacao de dados no Datalake](../transformacao-de-dados-no-datalake.md)

# Guia para migracao de procedures para dbt

- [Motivações](#motiva-es)
- [Guia](#guia)
  - [Reescrever UPDATE como CTE](#reescrever-update-como-cte)
  - [Substituir DELETE por flags](#substituir-delete-por-flags)
  - [Substituir INSERT](#substituir-insert)
    - [Se for um passo intermediário simples da procedure](#se-for-um-passo-intermedi-rio-simples-da-procedure)
    - [Caso seja materializado em tabela incremental](#caso-seja-materializado-em-tabela-incremental)
  - [Alternativas para tabelas temporárias](#alternativas-para-tabelas-tempor-rias)
    - [1. CTE](#key-1-cte)
    - [2. Ephemeral](#key-2-ephemeral)
    - [3. View](#key-3-view)
    - [4. Tabelas](#key-4-tabelas)
- [Referências](#refer-ncias)

---

---

# **Motivações**

Alguns benefícios que a mudança de procedures para modelos do dbt podem trazer:

- **Rastreabilidade:** Facilitará a identificação de onde vem cada informação, quem é o responsável e o histórico de modificações
- **Manutenibilidade:** Ao quebrar uma procedure de milhares de linhas em partes menores facilitará a manutenção, aumentando a velocidade de correções e melhorias.
- **Testabilidade:** Além disso, abre a possibilidade de inclusão de testes para garantir que uma alteração não mude uma regra de negócio e os dados estão de acordo com o esperado.

![](https://docs.getdbt.com/assets/images/dbt-diagram-cff121924c8e655e6cdd06e35a8852d2.png)

---

# **Guia**

## Reescrever UPDATE como CTE

Ao reescrever um UPDATE como CTE, uma das formas é substituir as condições do UPDATE por um CASE/WHEN. Dessa forma o resultado terá todas as linhas da tabela final, mas as linhas desejadas serão atualizadas.

Pontos de atenção:

- A CTE precisa retornar todas as linhas e não somente as que seriam afetadas no UPDATE

Exemplo:

**Procedure**

```sql
UPDATE A
SET AREA_PRODUTO = 'ASS/PP + HOST'
FROM UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC A
JOIN UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC B
	ON A.OFERTA = B.OFERTA
	AND A.AREA_PRODUTO = 'ASS/PP'
	AND B.AREA_PRODUTO = 'HOST';
```

**CTE**

```sql
WITH TMP_OFERTAS_YC_STEP_2 AS (
    SELECT A.* REPLACE (
        CASE
            WHEN B.AREA_PRODUTO = 'ASS/PP + HOST' THEN 'ASS/PP + HOST'
            ELSE A.AREA_PRODUTO
        END AS AREA_PRODUTO
    )
    FROM TMP_OFERTAS_YC A
    JOIN TMP_OFERTAS_YC B ON A.OFERTA = B.OFERTA
)

SELECT * FROM  TMP_OFERTAS_YC_STEP_2 
```

---

## Substituir DELETE por flags

Ao converter DELETEs de uma procedure, é possível trocar a condição do DELETE como uma cláusula WHERE diretamente no CTE, mas uma boa prática é adicionar uma flag nas linhas que precisam ser removidas, para depois fazer o filtro.

Exemplo:

**Procedure**

```sql
DELETE		A
FROM		UOL_YC.DBO.TBL_YC_INICIAL_AUX_1 A
WHERE		COD_OFERTA  IN (2885,2884) -- Ofertas de Domínios
AND			CARTEIRA = 'Não'
```

**CTE**

```sql
TBL_YC_INICIAL_AUX_STEP_2 AS (
    SELECT *,
        CASE
            WHEN COD_OFERTA IN (2885, 2884) AND CARTEIRA = 'Não' THEN TRUE
            ELSE FALSE
        END AS FL_DELETE
    FROM TBL_YC_INICIAL_AUX_STEP_1
)
SELECT * EXCEPT ( FL_DELETE )
FROM TBL_YC_INICIAL_AUX_STEP_2
WHERE FL_DELETE IS FALSE
```

---

## Substituir INSERT

### Se for um passo intermediário simples da procedure

Poderia substituir por um UNION

**Procedure**

```sql
INSERT INTO UOL_WORK.dbo.TMP_EMAILS_INVALIDOS_ELOQUA
SELECT DISTINCT
		FAT.IDT_PERSON,
		FAT.EMAIL,
		'EMAILS CONTEM ".com.b"' AS CLASSIFICACAO
FROM UOL_YC.DBO.TBL_FATO_USER_AUX FAT WITH(NOLOCK)
WHERE
		FAT.EMAIL LIKE '%.com.b'
AND FAT.IDT_PERSON NOT IN (SELECT DISTINCT IDT_PERSON FROM UOL_WORK.dbo.TMP_EMAILS_INVALIDOS_ELOQUA)
```

**CTE**

```sql
WITH TMP_EMAILS_INVALIDOS_ELOQUA_STEP_2 AS ( 
	SELECT DISTINCT
		FAT.IDT_PERSON,
		FAT.EMAIL,
		'EMAILS CONTEM ".com.b"' AS CLASSIFICACAO
	FROM UOL_YC.DBO.TBL_FATO_USER_AUX FAT WITH(NOLOCK)
	WHERE
		FAT.EMAIL LIKE '%.com.b'
	AND FAT.IDT_PERSON NOT IN (SELECT DISTINCT IDT_PERSON FROM UOL_WORK.dbo.TMP_EMAILS_INVALIDOS_ELOQUA)
)
SELECT * FROM TMP_EMAILS_INVALIDOS_ELOQUA
UNION ALL
SELECT * FROM TMP_EMAILS_INVALIDOS_ELOQUA_STEP_2
```

### Caso seja materializado em tabela incremental

Poderia substituir por um modelo incremental e utilizar uma unique\_key para identificar linhas a serem substituídas

**Procedure**

```sql
INSERT INTO UOL_WORK.dbo.TMP_EMAILS_INVALIDOS_ELOQUA
SELECT DISTINCT
		FAT.IDT_PERSON,
		FAT.EMAIL,
		'EMAILS CONTEM ".com.b"' AS CLASSIFICACAO
FROM UOL_YC.DBO.TBL_FATO_USER_AUX FAT WITH(NOLOCK)
WHERE
		FAT.EMAIL LIKE '%.com.b'
AND FAT.IDT_PERSON NOT IN (SELECT DISTINCT IDT_PERSON FROM UOL_WORK.dbo.TMP_EMAILS_INVALIDOS_ELOQUA)
```

**TMP\_EMAILS\_INVALIDOS\_ELOQUA.sql**

```sql
WITH TMP_EMAILS_INVALIDOS_ELOQUA AS ( 
	SELECT DISTINCT
		FAT.IDT_PERSON,
		FAT.EMAIL,
		'EMAILS CONTEM ".com.b"' AS CLASSIFICACAO
	FROM UOL_YC.DBO.TBL_FATO_USER_AUX FAT WITH(NOLOCK)
	WHERE
		FAT.EMAIL LIKE '%.com.b'
)
SELECT * FROM TMP_EMAILS_INVALIDOS_ELOQUA
```

**queries.yml**

```yml
    - name: TMP_EMAILS_INVALIDOS_ELOQUA
      dataset: dataset_exemplo_curated
      config:
        materialized: incremental
		incremental_strategy: merge
		unique_key: 
			- IDT_PERSON
      query_file: TMP_EMAILS_INVALIDOS_ELOQUA.sql
```

dbt vai pegar o resultado dessa query e rodar um comando MERGE usando a unike\_key. Se as linhas existirem no destino ele roda um UPDATE, caso contrário um INSERT.

Mais detalhes sobre modelos incrementais em: [Otimizacao com queries incrementais](otimizacao-com-queries-incrementais.md)

---

## Alternativas para tabelas temporárias

Existem algumas alternativas para o uso de tabelas temporárias e cada uma pode funcionar melhor dependendo do caso de uso.

### 1. CTE

A primeira opção e mais simples é utilizar CTE para representar uma tabela temporária.

- **Vantagem:** Mais simples, fica junto com a query principal
- **Desvantagem:** Se a query já estiver muito grande, talvez alguma outra alternativa funcione melhor

Exemplo:

**Procedure**

```sql
	SELECT	*
	INTO #OFERTAS_BOL_PAGO
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

	UPDATE		A
	SET			A.FL_BOL_PAGO = 'Sim'
	FROM		UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC A
	INNER JOIN	#OFERTAS_BOL_PAGO B
	ON			A.OFERTA = B.OFERTA
```

**TMP\_OFERTAS\_YC.sql**

```sql
WITH OFERTAS_BOL_PAGO AS (
    SELECT	*
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0
),
TMP_OFERTAS_YC_STEP_2 AS (
    SELECT A.* REPLACE (
        IFNULL(B.OFERTA, 'Sim', A.FL_BOL_PAGO) AS FL_BOL_PAGO
    )
    FROM TMP_OFERTAS_YC_STEP_1 A
    LEFT JOIN OFERTAS_BOL_PAGO B ON A.OFERTA = B.OFERTA
)
SELECT * FROM TMP_OFERTAS_YC_STEP_2
```

**queries.yml**

```yml
    - name: TMP_OFERTAS_YC
      dataset: dataset_exemplo_curated
      config:
        materialized: table
      query_file: query.sql
```

### 2. Ephemeral

O dbt suporta um tipo de materialização chamada ephemeral. Com ela é possível declarar as queries em arquivos diferentes e durante a execução ele troca a referência para o modelo pela query em formato de CTE.

- **Vantagem:** Query pode ser reutilizada com mais facilidade, sem precisar materializar no BigQuery
- **Desvantagem:** Não é possível rodar consultas diretamente no modelo ephemeral
- **Recomendação:** Não referencie modelos ephemerals em muitas queries, pois pode dificultar o debug de queries complexas. Nesses casos utilize alguma materialização como view ou tabela.

Exemplo:

**Procedure**

```sql
	SELECT	*
	INTO #OFERTAS_BOL_PAGO
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

	UPDATE		A
	SET			A.FL_BOL_PAGO = 'Sim'
	FROM		UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC A
	INNER JOIN	#OFERTAS_BOL_PAGO B
	ON			A.OFERTA = B.OFERTA
```

**OFERTAS\_BOL\_PAGO.sql**

```sql
    SELECT	*
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

```

**TMP\_OFERTAS\_YC.sql**

```sql
WITH OFERTAS_BOL_PAGO AS (
    SELECT	*
	FROM {{ ref('OFERTAS_BOL_PAGO') }} -- dbt irá substituir essa referência automaticamente pela query do arquivo "OFERTAS_BOL_PAGO.sql"
),
TMP_OFERTAS_YC_STEP_2 AS (
    SELECT A.* REPLACE (
        IFNULL(B.OFERTA, 'Sim', A.FL_BOL_PAGO) AS FL_BOL_PAGO
    )
    FROM TMP_OFERTAS_YC_STEP_1 A
    LEFT JOIN OFERTAS_BOL_PAGO B ON A.OFERTA = B.OFERTA
)
SELECT * FROM TMP_OFERTAS_YC_STEP_2
```

**queries.yml**

```yml
    - name:  OFERTAS_BOL_PAGO
      dataset: dataset_exemplo_curated
      config:
        materialized: ephemeral
      query_file: OFERTAS_BOL_PAGO.sql

	- name: TMP_OFERTAS_YC
      dataset: dataset_exemplo_curated
      config:
        materialized: table
      query_file: TMP_OFERTAS_YC.sql
```

### 3. View

Caso a lógica da tabela temporária possa ser reutilizada em outras queries ou mesmo facilitar um debug, uma alternativa é utilizar uma materialização como view.

- **Vantagem:** Query pode ser reutilizada com mais facilidade e ser executada diretamente
- **Desvantagem:** Views que fazem muita transformação ou usam tabelas muito grandes como fonte não são muito eficientes
- **Recomendação:** Caso a View seja referenciadas em muitas outras queries, avalie se não compensa materializar em tabelas para otimizar performance e custos

Exemplo:

**Procedure**

```sql
	SELECT	*
	INTO #OFERTAS_BOL_PAGO
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

	UPDATE		A
	SET			A.FL_BOL_PAGO = 'Sim'
	FROM		UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC A
	INNER JOIN	#OFERTAS_BOL_PAGO B
	ON			A.OFERTA = B.OFERTA
```

**OFERTAS\_BOL\_PAGO.sql**

```sql
    SELECT	*
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

```

**TMP\_OFERTAS\_YC.sql**

```sql
WITH OFERTAS_BOL_PAGO AS (
    SELECT	*
	FROM {{ ref('OFERTAS_BOL_PAGO') }} -- recomendado
	-- OU
	-- FROM `uolcs-datalake-prd.dataset_exemplo_curated.OFERTAS_BOL_PAGO`
),
TMP_OFERTAS_YC_STEP_2 AS (
    SELECT A.* REPLACE (
        IFNULL(B.OFERTA, 'Sim', A.FL_BOL_PAGO) AS FL_BOL_PAGO
    )
    FROM TMP_OFERTAS_YC_STEP_1 A
    LEFT JOIN OFERTAS_BOL_PAGO B ON A.OFERTA = B.OFERTA
)
SELECT * FROM TMP_OFERTAS_YC_STEP_2
```

**queries.yml**

```yml
    - name:  OFERTAS_BOL_PAGO
      dataset: dataset_exemplo_curated
      config:
        materialized: view
      query_file: OFERTAS_BOL_PAGO.sql

	- name: TMP_OFERTAS_YC
      dataset: dataset_exemplo_curated
      config:
        materialized: table
      query_file: TMP_OFERTAS_YC.sql
```

### 4. Tabelas

Uma última opção é simplesmente materializar a query como uma tabela. Nesse caso, também é possível **adicionar uma expiração** para que a tabela se auto delete após um intervalo de tempo.

- **Vantagem:** Mais eficientes para consultar, dados já estarão processados
- **Desvantagem:** Não reflete automaticamente dados da origem, precisa reprocessar para atualizar
- **Recomendação:** Priorize materializar como tabelas transformações complexas e dados que serão plugados no PowerBI

Exemplo:

**Procedure**

```sql
	SELECT	*
	INTO #OFERTAS_BOL_PAGO
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

	UPDATE		A
	SET			A.FL_BOL_PAGO = 'Sim'
	FROM		UOL_TEMPORARIO.dbo.TMP_OFERTAS_YC A
	INNER JOIN	#OFERTAS_BOL_PAGO B
	ON			A.OFERTA = B.OFERTA
```

**OFERTAS\_BOL\_PAGO.sql**

```sql
    SELECT	*
	FROM UOL.DBO.TB_PAINEL_OFERTAS A
	WHERE (A.AREA_SOLICITANTE = 'BOL'
	OR TEXTO_CURTO LIKE '%E-MAIL%BOL%'
	OR TEXTO_CURTO LIKE '%BOL%E-MAIL%'
	OR TEXTO_CURTO LIKE '%BOL%GIGAMAIL%'
	OR DESCRICAO LIKE 'E-MAIL PREMIUM BOL%')
	AND VALOR_OFERTA > 0

```

**TMP\_OFERTAS\_YC.sql**

```sql
WITH OFERTAS_BOL_PAGO AS (
    SELECT	*
	FROM {{ ref('OFERTAS_BOL_PAGO') }} -- recomendado
	-- OU
	-- FROM `uolcs-datalake-prd.dataset_exemplo_curated.OFERTAS_BOL_PAGO`
),
TMP_OFERTAS_YC_STEP_2 AS (
    SELECT A.* REPLACE (
        IFNULL(B.OFERTA, 'Sim', A.FL_BOL_PAGO) AS FL_BOL_PAGO
    )
    FROM TMP_OFERTAS_YC_STEP_1 A
    LEFT JOIN OFERTAS_BOL_PAGO B ON A.OFERTA = B.OFERTA
)
SELECT * FROM TMP_OFERTAS_YC_STEP_2
```

**queries.yml**

```yml
    - name:  OFERTAS_BOL_PAGO
      dataset: dataset_exemplo_curated
      config:
        materialized: table
	 	hours_to_expiration: 1 # (opcional) em quantas horas tabela será excluida
      query_file: OFERTAS_BOL_PAGO.sql

	- name: TMP_OFERTAS_YC
      dataset: dataset_exemplo_curated
      config:
        materialized: table
      query_file: TMP_OFERTAS_YC.sql
```

---

# **Referências**

**dbt**

- Curso gratuito: [Refactoring SQL for Modularity](https://courses.getdbt.com/courses/take/refactoring-sql-for-modularity)
- Artigo: [Migrating from Stored Procedures to dbt](https://docs.getdbt.com/blog/migrating-from-stored-procs)
- Guia: [Migrate from DDL, DML, and stored procedures](https://docs.getdbt.com/guides/migrate-from-stored-procedures?step=1)
- Guia: [Refactoring legacy SQL to dbt](https://docs.getdbt.com/guides/refactoring-legacy-sql?step=1)

**Caribe**

- [Transformacao de dados no Datalake](../transformacao-de-dados-no-datalake.md)
- [Otimizacao com queries incrementais](otimizacao-com-queries-incrementais.md)
