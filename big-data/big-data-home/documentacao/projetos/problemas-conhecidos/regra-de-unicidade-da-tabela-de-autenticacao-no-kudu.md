
[Documentação](../../../documentacao.md) > [Projetos](../../projetos.md) > [Problemas conhecidos](../problemas-conhecidos.md)

# Regra de unicidade da tabela de autenticacao no Kudu

> [!WARNING]
> Atabela raw\_authentication do Kudu utiliza como PK (Primary Key) as colunas:
>
> - dat\_authentication
> - idt\_person
>
> Quando uma autenticação é negada (username não encontrado, bloqueio por parte do Gaudi, etc), o idt\_person é preenchido com valor igual a 0.

Quando recebemos mais de um registro de autenticação negada(idt\_person = 0) com o mesmo timestamp, não teremos todos os logs na base mesmo que de usernames diferentes,  pois a regra de unicidade da PK irá manter apenas o primeiro registro, conforme exemplo abaixo:

|                                |                 |              |                                 |            |               |                   |                |                        |
|:-------------------------------|:----------------|:-------------|:--------------------------------|:-----------|:--------------|:------------------|:---------------|:-----------------------|
| **dat\_authentication**        | **idt\_person** | **username** | **domain**                      | **status** | **ip**        | **country\_name** | **city\_name** | **Importado pro Kudu** |
| January 3rd 2020, 16:00:17.100 | 0               | ef-miguel    | [uol.com.br](http://uol.com.br) | -7         | 144.217.249.2 | Canada            | Beauharnois    | **OK**                 |
| January 3rd 2020, 16:00:17.100 | 0               | neoradio     | [uol.com.br](http://uol.com.br) | -7         | 144.217.249.3 | Canada            | Beauharnois    | **NOK**                |
| January 3rd 2020, 16:00:17.100 | 0               | cesargaiotto | [uol.com.br](http://uol.com.br) | -7         | 51.79.60.165  | Canada            | -              | **NOK**                |

Essa questão será sanada na nova arquitetura que será disponibilizada em janeiro de 2020.
