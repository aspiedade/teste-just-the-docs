
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Redshift](../../redshift.md) > [Tutoriais](../tutoriais.md)

# Tutorial - Criar usuario local

Este é o passo-a-passo para a criação do usuário local. A autenticação no Redshift ainda não é via AD.

1 - Gerar o hash:

```java
SELECT md5('<password>' || '<usuario>');
```

2 - Criação do usuário (atentar-se ao grupo que o usuário será criado, nesse exemplo foi usado o g\_development):

```sql
CREATE USER <usuario>
    PASSWORD 'md5HASH'
    NOCREATEDB NOCREATEUSER SYSLOG ACCESS RESTRICTED
    IN GROUP g_development 
    CONNECTION LIMIT 9;
```

Sobre os grupos: [Filas workload management WLM](../filas-workload-management-wlm.md)

3 - Inclusão no grupo local, referente ao time do usuário, por exemplo g\_delta\_dev:

```sql
ALTER GROUP <nome-grupo> ADD USER <usuario>;
```

4 - Commit no stash:

<https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/browse/redshift/datalake/sys>

É importante acrescentar no commit o id do chamado criado no portal <https://jira.intranet.uol.com.br/jira/projects/SDBD/queues/custom/1760>

Evitar comitar o código contendo o hash.

Se for necessário criar o grupo de um time novo e dar os devidos grants:

```sql
CREATE GROUP <nome-grupo>;

-- USAGE
GRANT USAGE ON SCHEMA <nome-schema> TO GROUP <nome-grupo>;

-- GRANT NAS TABELAS
GRANT SELECT ON ALL TABLES IN  SCHEMA <nome-schema> TO GROUP <nome-grupo>;

-- GRANT NAS NOVAS TABELAS 
ALTER DEFAULT PRIVILEGES IN SCHEMA <nome-schema> GRANT SELECT ON TABLES TO GROUP <nome-grupo>;
```
