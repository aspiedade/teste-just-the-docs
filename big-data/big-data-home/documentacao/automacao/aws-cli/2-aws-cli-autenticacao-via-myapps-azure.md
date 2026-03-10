
[Documentação](../../../documentacao.md) > [Automacao](../../automacao.md) > [AWS CLI](../aws-cli.md)

# 2 - AWS CLI - autenticacao via MyApps Azure

Pré-requisitos para executar os procedimento descritos nesta página:

 Executar os comandos através do Windows e ter o AWS CLI instalado, que está documentado em : [1- AWS CLI - instalacao](1-aws-cli-instalacao.md)

1. Instale a última versão do [Node.js](https://nodejs.org/en/)
2. Através do Node, instale o aws-azure-login

   ```bash
   npm install -g aws-azure-login
   ```
3. Configure o perfil default

   ```bash
   aws-azure-login --configure

   Configuring profile 'default'
   ? Azure Tenant ID: 7575b092-fc5f-4f6c-b7a5-9e9ef7aca80d
   ? Azure App ID URI: urn:amazon:webservices
   ? Default Username: <email>@uolinc.com
   ? Default Role ARN (if multiple):
   ? Default Session Duration Hours (up to 12): 1
   Profile saved.



   ```
4. Autenticação -  no cmd, digite os seguintes comandos

   ```bash
   aws-azure-login --mode cli --no-sandbox

   Logging in with profile 'default'...
   Using AWS SAML endpoint https://signin.aws.amazon.com/saml
   ? Username: <user>@uolinc.com
   ? Password: [hidden]
   ? Session Duration Hours (up to 12): 1
   Assuming role arn:aws:iam::420409533776:role/G_uol_aws_dev_bigdata


   ```

Referências

<https://www.npmjs.com/package/aws-azure-login>

[AWS Cli com User Federated (myapps Azure)](/confluence/pages/viewpage.action?pageId=200475364)
