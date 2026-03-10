
[Documentação](../../documentacao.md) > [Projetos](../projetos.md)

# Inferencia de Acesso

## Introdução

Projeto cuja finalidade é capturar e armazenar os dados relacionados a Autorização e Sessão, e hoje contempla somente o Webmail, e carregar em um banco de dados para consultas.

## Deficiência

Era difícil de saber quando o cliente acessou o produto, só viam quando o cliente autenticou.

## Descrição

Quando tem um processo civil, o delegado e juiz pedem os logs dos clientes afetados, o departamento jurídico conversa com o time de CSIRT. Quando solicitados, os logs eram buscados, fazendo "grep" no terminal, diversos trabalhos efetuados manualmente para filtrar o login, e o período para enviar ao departamento jurídico. Agora liberamos acesso as áreas para consultas com período e login. Com a inferencia sabemos o data/hora de acesso do produto, a respeito de sessão e autorização.

## Arquivos

```
  

```

Arquivos do projeto:

```
.
├── airflow
│   ├── dag
│       └── inferencia_to_redshift.py
|       ├── subdag
|           └── gaudi_authorization_log_s3_to_s3stg.py
|           └── session_checksessions_log_s3_load_partition.py
├── athena
│   └── athena.sql
├── redshift
│   └── redshift.sql
├── spark
│   └── gaudi_authorization_parse.py
│   └── inferencia_acesso.py
└── README.md  
  

```

## Erros

- Erros de execução podem ser analisados diretamente nos logs do Airflow quando alarmados no Teams;

## Regra de Inferência

A seguinte regra de cruzamento entre os modelos de Autorização e Sessão foi decidida para fazer a inferência de acesso ao produto:  
1. Filtrar os access\_id(principal) de Webmail abaixo no log de autorização;

- 98465ec6eae645db9a3c43382a2defdd
- Fd1043410d2f6bcbf03e46e4cb87f1a6

2. Ainda no log de autorização, validar se no vetor de autorização existe ao menos uma entrada com status=1 (autorizado), para idt\_service= 8 e 9;  
3. Verificar a sessão a partir do access\_id(principal) e idt\_person;  
4. Procurar sessões válidas (responseStatus = 2XX) nos próximos 3s e 500ms à frente;

## Dicionário de Dados

### View DATA LAKE - customer\_access:  v\_inferencia\_acesso - View referente aos logs da Inferência

|  Field                    |  Description                                                                        |
|:--------------------------|:------------------------------------------------------------------------------------|
| `access_id`               | Access ID da aplicacao que chamou o servidor de sessao                              |
| `access_id_desc`          | descricao do accessId/produto                                                       |
| `dat_lake_in`             | Timestamp de chegada no datalake do log em America/Sao\_Paulo                       |
| `date_session`            |                                                                                     |
| `idt_person`              | ID do usuário na plataforma                                                         |
| `login`                   | Login do usuario da plataforma                                                      |
| `opname`                  | Nome da operação                                                                    |
| `requestid_authorization` | Id da requisição de autorização                                                     |
| `requestid_session`       | ID da requisicao do servidor de sessao                                              |
| `responsestatus`          | Status da requisicao de sessao                                                      |
| `status_autorizacao`      | 1: autorizado                                                                       |
| `status_inferencia`       | OK: acessou / FAIL: Tentativa inválida                                              |
| `timestamp_session`       | Timestamp de chegada no datalake do log em UTC                                      |
| `timestamp_tz`            | timezone da coluna timestamp\_session                                               |
| `xforwardedfor`           | Vetor de IPs sendo a primeira posição o IP do cliente e o retante de hosts internos |
| `xforwardedsourceport`    | Porta de acesso do cliente                                                          |
| `xrequestid`              | ID da requisicao unica entre sessao e autorizacao                                   |

### Tabela customer\_access.inferencia\_acesso: tabela referente a inferência dos Logs de Sessão e de Autorização

|  Field                    |  Description                                                                        |
|:--------------------------|:------------------------------------------------------------------------------------|
| `requestid_session`       | ID da requisicao do servidor de sessao                                              |
| `xrequestid`              | ID da requisicao unica entre sessao e autorizacao                                   |
| `opname`                  | Nome da operação                                                                    |
| `access_id`               | Access ID da aplicacao que chamou o servidor de sessao                              |
| `idt_person`              | ID do usuário na plataforma                                                         |
| `timestamp_session`       | Timestamp de chegada no datalake do log em UTC                                      |
| `date_session`            |                                                                                     |
| `xforwardedfor`           | Vetor de IPs sendo a primeira posição o IP do cliente e o retante de hosts internos |
| `xforwardedsourceport`    | Porta de acesso do cliente                                                          |
| `responsestatus`          | Status da requisicao de sessao                                                      |
| `status_autorizacao`      | 1: autorizado                                                                       |
| `requestid_authorization` | Id da requisição de autorização                                                     |
| `status_inferencia`       | OK: acessou / FAIL: Tentativa inválida                                              |
| `login`                   | Login do usuario da plataforma                                                      |
| `access_id_desc`          | descricao do accessId/produto                                                       |
| `timestamp_tz`            | timezone da coluna timestamp\_session                                               |
| `dat_lake_in`             | Timestamp do cálculo de inferencia em America/Sao\_Paulo                            |
