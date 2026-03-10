
[Documentação](../../../documentacao.md) > [Azure](../../azure.md) > [POC Cloudera](../poc-cloudera.md)

# Impala query editor

Sempre que o metadado de uma tabela for alterado fora do Impala query editor,como exclusão de arquivo de dado direto data lake store, é necessário atualizar o catalogo de metadados, para isso execute o comando:

```
INVALIDATE METADATA [[db_name.]table_name];
```

Ou seja, se for necessário atualizar os metadados de todas as tabelas, é só mandar:

```
INVALIDATE METADATA;
```

Para apenas a tabela tabteste:

```
INVALIDATE METADATA tabteste;
```
