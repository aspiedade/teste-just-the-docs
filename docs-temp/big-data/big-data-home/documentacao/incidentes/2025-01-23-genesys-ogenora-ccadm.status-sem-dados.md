
[Documentação](../../documentacao.md) > [Incidentes](../incidentes.md)

# 2025-01-23 - Genesys-Ogenora - CCADM.STATUS sem dados

## Data

2025-01-23

## Autores

- Damião Martins

## Status

Normalizado

## Resumo

Deixamos de receber dados da tabela CCADM.STATUS via OGG. Extrator e replicat estavam funcionando normalmente, o erro foi identificado pelo source\_freshness da tabela. Problema foi causado por uma falha na aplicação que popula o Oracle Ogenora.

## Timeline

2025-01-22 18h51: Primeiro alarme de source\_freshness da tabela **genesys\_ogg\_ogenora\_ccadm\_raw.status**

2025-01-23 10h30: Verificamos que a tabela do BigQuery estava igual à tabela no Ogenora CCADM.STATUS, então não foi um erro na replicação

2025-01-23 11h17: Enviado email para time l-oper-genesys

2025-01-23 14h41: Elias (ekyoharu) respondeu dizendo que processo havia normalizado as 14h05

## Causa raiz

Segundo **ekyoharu** por email (Thread: Estrutura CCADM.STATUS (LOGIN / LOGOUT) sem dados desde ontem - 22/01)

**"**Tivemos um problema na aplicação responsável pela gravação dos dados da Login\_Detalhado e portanto os dados não foram gravados, o problema foi normalizado hoje às 14h05 após algumas ações realizadas pelo time, lembrando que esta aplicação é legada e não nenhum tipo de suporte do fabricante;"

## Resolução

Acionado time **l-oper-genesys** via email.

## Correções e medidas preventivas

N/D

## Ocorrências

- 2025-01-23
- 2025-03-24 - Email: "[GENESYS] - Atraso na entrega dos dados de LOGIN DETALHADO"
