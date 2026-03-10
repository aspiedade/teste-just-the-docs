
[Documentação](../../documentacao.md) > [Incidentes](../incidentes.md)

# 2025-12-15: Postmortem: OGG - Falha na replicacao de dados UOLBR

## Data

2025-12-15

## Autores

- Damião Martins

## Status

Resolvido

## Resumo

Paramos de receber dados do UOLBR pelo OGG, que impactou em todos os relatórios que usam dados de cadastro e assinaturas.

## Timeline

2025-12-12 23h03: Primeiro erro de freshness na tabela INSCRIPTION

2025-12-15 09h10: Analytics envia email perguntando sobre atraso dos dados

2025-12-15 09h40: Julia acionou Angeli para ajudar a verificar problema. Identificado que os dados do UOLBR não estavam chegando pelo OGG.

2025-12-15 10h00: Acionado DBAs para verificarem problema na replicação

2025-12-15 10h30: Cezar consegue restabelecer processo

2025-12-15 11h43: Dados atrasados terminam de ser replicados para BQ

2025-12-15 12h43: Reprocessado todas as DAGs RAW

2025-12-15 13h54: Reprocessado todas as DAGs CURATED

## Causa raiz

Segundo Cezar, problema ocorreu por conta de um script python na a2-ogate1 que começou a dar erro ao importar a lib "requests".

Esse script é responsável por monitorar o lag do extrator e enviar o alerta. Como o script dava erro na execução, não verificava o lag nem conseguia enviar o alarme.

## Resolução

Reiniciado o extrator EXT\_U7BQ.

## Correções e medidas preventivas

DBAs:

- Adicionado tratamento de erro no script de monitoria

D&A

- Vamos adicionar um alarme de *file age* nos arquivos de trail para conseguir identificar falhas na replicação mais rapidamente

---

## Referências
