
[Documentação](../../documentacao.md) > [Incidentes](../incidentes.md)

# 2025-03-13: Postmortem: Instabilidade internet Jenkins

## Data

2025-03-13

## Autores

- Damião Martins

## Status

Resolvido

## Resumo

Máquina do Jenkins perdeu acesso à internet

## Timeline

2025-03-13 2h30: Primeirou job deu erro de timeout de rede (<https://jenkins-dados.data.intranet/job/Chicago/job/DMP/job/QueryMakerPublisher/543/>)

2025-03-13 11h58: Abertura de chamado
[[SDINFRA-50392] Falha de Virtualização](https://jira.intranet.uol.com.br/jira/browse/SDINFRA-50392)

2025-03-13 12h13: Resolvido chamado, acesso reestabelecido.

## Causa raiz

Segundo comentário no chamado:
[[SDINFRA-50392] Falha de Virtualização](https://jira.intranet.uol.com.br/jira/browse/SDINFRA-50392)

*Decorrente do chaveamento que ocorreu ontem [SDINFRA-50010](https://jira.intranet.uol.com.br/jira/browse/SDINFRA-50010 "Chaveamento de T1-PROD-TIME para o novo T1-GERAL-PROD.")*  
*Falha identificada e corrigida.*

*Desativado dnats e snats do t1 antigo.*

## Resolução

Chamado para equipe de Infra:
[[SDINFRA-50392] Falha de Virtualização](https://jira.intranet.uol.com.br/jira/browse/SDINFRA-50392)

## Correções e medidas preventivas

---

## Referências
