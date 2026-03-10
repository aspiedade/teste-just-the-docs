
[Documentação](../../../documentacao.md) > [AWS](../../aws.md) > [Data Lake](../data-lake.md)

# Metabase

**URL:**

<https://metabase.intranet:3000/>

Acessar com e-mail e senha de rede.

**Exploração dos Dados**

Para que uma tabela ou view do **Redshift** possa ser explorada via Metabase, o usuário **metabaseubr** precisa ter grant nela.

Exemplo:

```sql
GRANT USAGE ON SCHEMA gaudi TO metabaseubr;
GRANT SELECT ON gaudi.v_authentication_last_year TO metabaseubr




```
