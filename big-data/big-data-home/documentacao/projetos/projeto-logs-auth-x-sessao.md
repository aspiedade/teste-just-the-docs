
[Documentação](../../documentacao.md) > [Projetos](../projetos.md)

# Projeto Logs Auth x Sessao

## Introdução

Projeto cuja finalidade é capturar e armazenar os dados relacionados a Autorização e Sessão, e tendo como principal cliente o departamento jurídico, mais específicamente Webmaill e carregar em um banco de dados para consultas.

## Deficiência

Era difícil de saber quando o cliente acessou o produto, só viam quando o cliente autenticou.

## Descrição

Quando tem um processo civil, o delegado e juiz pedem os logs dos clientes afetados, o departamento jurídico conversa com o time de CSIRT. Os logs eram buscados, fazendo "grep" no terminal, diversos trabalhos efetuados manualmente para filtrar o login, e o período para enviar ao departamento jurídico. Agora liberamos acesso as áreas para consultas com período e login. Com a inferencia sabemos o data/hora de acesso do produto, a respeito de sessão e autorização.

## Arquivos

```
  

```

Arquivos do projeto:

```
  

```

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

## Dicionário de Dados

### Tabela session\_raw.session\_checksessions\_log: tabela referente aos logs de Sessão

| Field                  | Description                                                                         |
|:-----------------------|:------------------------------------------------------------------------------------|
| `accessId`             | Access ID da aplicacao que chamou o servidor de sessao                              |
| `xForwardedSourcePort` | Porta de acesso do cliente                                                          |
| `opName`               | Nome da operação                                                                    |
| `awsRequestId`         | ID da requisicao do servidor de sessao                                              |
| `xRequestId`           | ID da requisicao unica entre sessao e autorizacao                                   |
| `ip`                   | IP do host interno que enviou a solicitacao                                         |
| `idtPerson`            | ID do usuario na plataforma                                                         |
| `dat_lake_in`          | Timestamp de chegada no datalake do log em America/Sao\_Paulo                       |
| `userAgent`            | Descrição dos navegadores                                                           |
| `xForwardedFor`        | Vetor de IPs sendo a primeira posição o IP do cliente e o retante de hosts internos |
| `responseStatus`       | Status da requisicao de sessao                                                      |
| `deviceId`             | ID do device da plataforma DNA                                                      |
| `timestamp`            | Timestamp de chegada no datalake do log em UTC                                      |
| `year`                 | Ano da coluna timestamp                                                             |
| `month`                | Mes da coluna timestamp                                                             |
| `day`                  | Dia da coluna timestamp                                                             |
| `hour`                 | Hora da coluna timestamp                                                            |

### Tabela gaudi\_stg.gaudi\_authorization\_log: tabela referente aos logs de Autorização

| Field                              | Description                                                                                          |
|:-----------------------------------|:-----------------------------------------------------------------------------------------------------|
| `requestId`                        | Id da requisição de autorização                                                                      |
| `host`                             | Vetor de hosts que enviaram os logs para compor a linha                                              |
| `uri`                              | URI com o método do gaudi que foi chamado                                                            |
| `idt_person`                       | ID do usuário na plataforma                                                                          |
| `dat_lake_in_max`                  | Timestamp de chegada no datalake do primeiro log utilizado para compor a linha em America/Sao\_Paulo |
| `dat_lake_in_min`                  | Timestamp de chegada no datalake do ultimo log utilizado para compor a linha em America/Sao\_Paulo   |
| `timestamp_max`                    | Timestamp de chegada no logstash do primeiro log utilizado para compor a linha em America/Sao\_Paulo |
| `timestamp_min`                    | Timestamp de chegada no logstash do ultimo log utilizado para compor a linha em America/Sao\_Paulo   |
| `time_min`                         | Timestamp do primeiro log utilizado para compor a linha em America/Sao\_Paulo                        |
| `time_max`                         | Timestamp do ultimo log utilizado para compor a linha em America/Sao\_Paulo                          |
| `authorization`                    | Array                                                                                                |
| `authorization.idt_service`        | ID do serviço na plataforma                                                                          |
| `authorization.status_autorizacao` | 1: autorizado                                                                                        |
| `authorization.idt_product`        | Vetor com ID de todos os produtos na plataforma que foram testados para o idt\_service               |
| `principal`                        | Access ID da aplicacao que chamou o servico de autorizacao                                           |
| `dt_year`                          | Ano do campo time\_max                                                                               |
| `dt_month`                         | Mês do campo time\_max                                                                               |
| `dt_day`                           | Dia do campo time\_max                                                                               |
| `dt_hour`                          | Hora do campo time\_max                                                                              |

### Tabela customer\_access.inferencia\_acesso: tabela referente a inferência dos Logs de Sessão e de Autorização

| Field                     | Description                                                                         |
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

```
  

```
