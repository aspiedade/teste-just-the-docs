
[Documentação](../../../documentacao.md) > [Padroes e Diretrizes Tecnicas](../../padroes-e-diretrizes-tecnicas.md) > [Deploy de Componentes](../deploy-de-componentes.md)

# ADR - Jenkins

**Título**

Essa decisão define o uso do Jenkins pelos times de D&A.

**Status**

Aceita

**Data**

2023-03-03

**Contexto**

O Jenkins já tem sido muito usado em diversas equipes dentro de D&A, porém cada time tem seu próprio Jenkins.

O Jenkins de Big Data (BIBD), possui views separadas para os times caribe, kansas, delta e chicago, sendo que alguns jobs já foram entregues por ele.

**Decisão**

Vamos utilizar somente um Jenkins para todas as equipes, então todas as novas entregas devem ser feitas pelo Jenkins “BIBD”.

Salvo exceção, alguma entrega poderá ser feita em outro Jenkins já existente, porém esse caso deve gerar uma demanda de migração futura.

**Consequências**

Positiva:

- Somente uma infra para manter e fazer upgrades
- Um time pode mapear melhorias que beneficiará outro

Negativa:

- Será necessário dispender tempo das equipes para futuramente migrar pipelines que já existem em outro Jenkins.

**Compliance**

-

**Notas**

- Author: Samuel
- Version: 0.2
- Changelog:

  - 0.1: Versão inicial proposta
  - 0.2: Versão aceita

Referência: <https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/appendix.html>
