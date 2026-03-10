
[Documentação](../../../documentacao.md) > [Projetos](../../projetos.md) > [Problemas conhecidos](../problemas-conhecidos.md)

# Limitacoes dos componentes

## Hadoop (HDFS/Impala)

> [!CAUTION]
> **Schema**
>
> - Não suporta indices (nem PK, secundários e muito menos FK)
> - Não permite alteração/exclusão de dados que foram armazenados , pois trabalha com conceito de armazenamento arquivo, e uma vez armazenado só permite a exclusão física do arquivo (e consequentemente todos os dados contidos nele)
>
> **Data Redaction**
>
> - possui essa feature mas é bem limitada, que aplica um regex no dado e não no metadado (não dá p/ aplicar em uma coluna ou tabela específica)
> - não suporta roles, ou alguma forma que permita não aplicar o mascaramento para alguns usuários
> - não suporta mascaramento dinâmico personalisado (vai sempre substituir o valor full por uma string pre-definida)
>
> **Regular Expression**
>
> - O Impala utiliza expressões regulares da biblioteca Google RE2, e tem muitas expressões que (ainda) não são suportadas. Documentação em <https://github.com/google/re2/blob/master/doc/syntax.txt>

## Apache Kudu

> [!WARNING]
> **Scale**
>
> - O número de master servers no cluster precisa ser ímpar, sendo que o máximo recomendado é 3
> - O número máximo de tablet servers recomendado é 100
> - Um tablet server comporta no máximo 2000 tablets (post-replication), mas o recomendado é de até 1000 tablets por tablet server
> - Cada tabela pode ter no máximo 60 tablets (post-replication)
> - É recomendado que o volume total de dados armazenados (post-replication e post-compression) seja inferior a 8TB por tablet server

> [!CAUTION]
> **Schema**
>
> - A definição da chave primária é obrigatória na criação da tabela, e não pode ser alterada. Caso isso seja necessário, é preciso dropar e recriar a tabela com a nova estrutura de PK #tenso
> - As colunas que compõe a PK devem sempre ser as primeiras no comando de create table
> - Não surporta indices secundários nem foreign keys
>
> **Tables**
>
> - O número de réplicas de uma tabela deve ser ímpar, sendo que o máximo permitido é 7
> - O fator de replicação é definido na criação da tabela e não pode ser alterado
>
> **Backup**
>
> - O Kudu ainda não possui uma feature para backup e restore (e como os dados são replicados para outros nodes do cluster, fazendo com que a perda de uma maquina não gere danos, eles não estão priorizando isso). Por enquanto eles recomendam usar o Spark ou Impala para fazer o export/import das tabelas conforme a necessidade
>
> Mais informações em : <https://kudu.apache.org/docs/known_issues.html>
