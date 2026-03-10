
[Documentação](../documentacao.md)

# Migracao Stash > Github

### Guia oficial para a migração de repositórios do Stash, para o GitHub.

#### Este documento tem como objetivo fornecer um passo a passo para garantir que a migração dos projetos seja feita de forma padronizada, segura e rápida.

#### Siga os passos abaixo para realizar a migração completa de seus repositórios.

---

### **Passo 1 - Abrir chamado para criação de repositório no GitHub**

Nesta seção, será detalhado o processo de solicitação formal para a criação de um novo repositório no ambiente do GitHub da organização.

- **1.1. Acessando o portal de chamados**
- **1.2. Preenchendo as informações necessárias**

  - Nome do time
  - Nome do projeto/produto
  - Justificativa
- **1.3. Acompanhando a solicitação**

---

### **Passo 2 - Padrões de nomenclatura do repositório**

Para manter nossa organização de projetos consistente, é crucial seguir os padrões de nomenclatura estabelecidos.

- **2.1. Nomenclatura para Aplicações**
- **2.2. Atalhos para visualização do time**

---

### **Passo 3 - Alterações de apontamento do Jenkinsfile**

Com o novo repositório no GitHub, será necessário atualizar o `Jenkinsfile` de seus projetos para que o pipeline de CI/CD continue funcionando corretamente.

- **3.1. Alterando a seção `scm` para o novo repositório**
- **3.2. Configurando gatilhos (triggers) para o GitHub**
- **3.3. Validando o pipeline após a alteração**

---

### **Passo 4 - Arquivar repositório antigo no Stash**

Após garantir que o novo repositório no GitHub está funcional e o pipeline está operando como esperado, o último passo é arquivar o repositório antigo no Stash.

- **4.1. Checklist de verificação pré-arquivamento**
- **4.2. Como arquivar o repositório no Stash**

---

### **Extra - Dicas e Atalhos do GitHub**

```
Adicionar aqui quaisquer informações ou tips interessantes sobre utilização do github que acharmos durante a migração.
```

- **5.1. Exemplo 1**

---

### **Acompanhamento**

**Caribe ( Exemplo )**

| Nome da Aplicação ( Stash )   | Nome da Aplicação ( Github )                | Status Migração   | Data da migração   |
|:------------------------------|:--------------------------------------------|:------------------|:-------------------|
| app-caribe-transformer        | uolcs-data-analytics-caribe-transformer     | TO DO <br/>       | - <br/>            |
| app-caribe-batch-pipelines    | uolcs-data-analytics-caribe-batch-pipelines | WIP <br/>         | - <br/>            |
| app-caribe-schema-files       | uolcs-data-analytics-caribe-schema-files    | DONE <br/>        | 2026-03-04 <br/>   |
