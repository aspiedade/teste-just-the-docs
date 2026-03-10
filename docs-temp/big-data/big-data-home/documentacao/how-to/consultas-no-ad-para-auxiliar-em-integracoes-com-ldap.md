
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Consultas no AD para auxiliar em integracoes com LDAP

As integrações com o LDAP estão sendo feitas utilizando o usuário de serviço "svcacc\_bigdata" e grupo "g\_s\_p\_bigdata\_X".

Se você precisar criar um outro usuário de serviço, ou um grupo no AD, mais especifico para suas necessidades, [clique aqui](https://jira.intranet.uol.com.br/jira/servicedesk/customer/portal/45/create/783) para abrir uma chamado para o suporte corporativo solicitando a criação.

## Listar informações do Grupo

Este comando é util para saber quem são os membros do grupo

**net group GROUP\_NAME\_HERE /domain**

```java
Exemplo:
net group g_s_p_bigdata_X /domain
```

**Resultado**

```java
The request will be processed at a domain controller for domain uolcorp.intranet

Group name g_s_p_bigdata_X
Comment

Members
-------------------------------------------------------------------------------
bfarias  cdolacio        cin_flombardi
cverni   ess_dsiqueira   ess_rnogueira
mmsilva  peo_ebucci      peo_mmizokami
rkakara  sfraga          pgs_whenrique
ymelo
```

## Listar informações de um Usuário

Este comando é util para verficar se um usuário está Ativo ou Bloqueado, os grupos ao qual ele pertence, etc

**net user USERNAME\_HERE /domain**

```java
Exemplo:
net user svcacc_bigdata /domain
```

**Resultado**

```java
The request will be processed at a domain controller for domain uolcorp.intranet.

User name                    svcacc_bigdata
Full Name                    svcacc_bigdata
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            ‎26/‎02/‎2020 10:22:15
Password expires             Never
Password changeable          ‎27/‎02/‎2020 10:22:15
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   Never

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *G_s_jira_sd_customers*Domain Users
The command completed successfully.
```
