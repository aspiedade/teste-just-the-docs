
[Documentação](../../documentacao.md) > [Padroes e Diretrizes Tecnicas](../padroes-e-diretrizes-tecnicas.md)

# [Rascunho] ADR - Gestao de acessos

- [Resumo](#resumo)
- [Log](#log)
  - [Materiais de conversas anteriores:](#materiais-de-conversas-anteriores)
- [Visão geral dos processos a serem automatizados](#vis-o-geral-dos-processos-a-serem-automatizados)
- [Processos mapeados por GOV](#processos-mapeados-por-gov)
  - [1. Fluxos de aprovação de acesso ao conteúdo de outros domínios/subdomínios](#key-1-fluxos-de-aprova-o-de-acesso-ao-conte-do-de-outros-dom-nios-subdom-nios)
    - [1.2. Fluxo de aprovação de acesso ao conteúdo de outros domínios (Datalake) - com Automação](#key-1-2-fluxo-de-aprova-o-de-acesso-ao-conte-do-de-outros-dom-nios-datalake-com-automa-o)
  - [2. Fluxos de aprovação de acesso ao conteúdo sem mascaramento (sensitive data e confidential) - a nível de policy tag](#key-2-fluxos-de-aprova-o-de-acesso-ao-conte-do-sem-mascaramento-sensitive-data-e-confidential-a-n-vel-de-policy-tag)
    - [2.1. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (sensitive data) no projeto - com Automação](#key-2-1-fluxo-de-aprova-o-de-acesso-ao-conte-do-sem-mascaramento-sensitive-data-no-projeto-com-automa-o)
    - [2.2. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (confidential) no projeto (Datalake) - com Automação](#key-2-2-fluxo-de-aprova-o-de-acesso-ao-conte-do-sem-mascaramento-confidential-no-projeto-datalake-com-automa-o)
  - [3. Fluxos de aprovação de acesso ao conteúdo sem mascaramento (PII/PCI) - a nível de taxonomia](#key-3-fluxos-de-aprova-o-de-acesso-ao-conte-do-sem-mascaramento-pii-pci-a-n-vel-de-taxonomia)
    - [3.1. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (PII/PCI) no projeto (Datalake) - com Automação](#key-3-1-fluxo-de-aprova-o-de-acesso-ao-conte-do-sem-mascaramento-pii-pci-no-projeto-datalake-com-automa-o)
  - [4. Fluxos de aprovação de criação de novo (usuário/projeto/grupo)](#key-4-fluxos-de-aprova-o-de-cria-o-de-novo-usu-rio-projeto-grupo)
    - [4.1. Fluxo de aprovação de acesso do novo usuário (Metadados + Grupo) - sem Automação](#key-4-1-fluxo-de-aprova-o-de-acesso-do-novo-usu-rio-metadados-grupo-sem-automa-o)
    - [4.2. Fluxo de aprovação de criação novo projeto (Datalab) - sem Automação](#key-4-2-fluxo-de-aprova-o-de-cria-o-novo-projeto-datalab-sem-automa-o)
    - [4.3. Fluxo de aprovação de criação novo grupo do AD (domínio ou subdomínio) na GCP - sem Automação](#key-4-3-fluxo-de-aprova-o-de-cria-o-novo-grupo-do-ad-dom-nio-ou-subdom-nio-na-gcp-sem-automa-o)

# **Resumo**

Este documento servirá para registrar as definições de melhorias nos processos de acesso aos dados do Datalake.

# **Log**

2024-03-26: Inicio

## **Materiais de conversas anteriores:**

- [AP11CAR-484](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-484)
- [Fluxos Gestão Acesso (Sharepoint)](https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/GCP.aspx?ct=1695927127377&or=Teams-HL&ga=1)

# **Visão geral dos processos a serem automatizados**

**Fluxos**

- [ ] Criação de novo datalab e grupo de acesso
- [ ] Setup de novos projetos (IAM, quotas, audit logs etc) → Existem novos projetos que não passaram por Caribe para fazer setup, podem estar sem quota e não temos logs de uso
- [ ] Inclusão de usuários em grupos de acesso
- [ ] Aprovação de acesso aos dados
- [ ] Permissão de acesso aos dados no dataplex

**O que precisamos definir em cada fluxo**

- [ ] Priorização
- [ ] Quem será responsável por automatizar
- [ ] Quem ficará responsável pelo fluxo
- [ ] Quem serão os responsáveis pro aprovação

# **Processos mapeados por GOV**

## 1. Fluxos de aprovação de acesso ao conteúdo de outros domínios/subdomínios

### 1.2. Fluxo de aprovação de acesso ao conteúdo de outros domínios (Datalake) - com Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-acesso-ao-conte%c3%bado-de-outros-dom%c3%adnios-(Datalake)(1).aspx?ga=1>

## 2. Fluxos de aprovação de acesso ao conteúdo sem mascaramento (sensitive data e confidential) - a nível de policy tag

### 2.1. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (sensitive data) no projeto - com Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-acesso-ao-conte%c3%bado-sem-mascaramento-(sensitive-data)-no-projeto-(Datalake).aspx?ga=1>

### 2.2. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (confidential) no projeto (Datalake) - com Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%C3%A7%C3%A3o-de-acesso-ao-conte%C3%BAdo-sem-mascaramento-(confidential)-no-projeto-(Datalake).aspx?csf=1&web=1&share=EdM2kipNLrFOvDQQEjBHDGkBJogR170mgJhVT4qvfWw7Fg&e=QkZkPN&cid=f34eae78-6f65-493c-a8c8-aaea88552137>

## 3. Fluxos de aprovação de acesso ao conteúdo sem mascaramento (PII/PCI) - a nível de taxonomia

### 3.1. Fluxo de aprovação de acesso ao conteúdo sem mascaramento (PII/PCI) no projeto (Datalake) - com Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-acesso-ao-conte%c3%bado-sem-mascaramento-(PII-PCI-Pseudoanonymized)-no-projeto-(Datalake).aspx?ga=1>

## 4. Fluxos de aprovação de criação de novo (usuário/projeto/grupo)

### 4.1. Fluxo de aprovação de acesso do novo usuário (Metadados + Grupo) - sem Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-acesso-(novo-usu%c3%a1rio).aspx?ga=1>

### 4.2. Fluxo de aprovação de criação novo projeto (Datalab) - sem Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-acesso-ao-projeto-(existente).aspx?ga=1>

### 4.3. Fluxo de aprovação de criação novo grupo do AD (domínio ou subdomínio) na GCP - sem Automação

<https://uolinc.sharepoint.com/sites/GovernancadeDados/SitePages/Fluxo-de-aprova%c3%a7%c3%a3o-de-cria%c3%a7%c3%a3o-novo-grupo-(dom%c3%adnio-ou-subdom%c3%adnio)-na-GCP.aspx?ga=1>
