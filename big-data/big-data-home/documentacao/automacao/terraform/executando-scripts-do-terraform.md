
[Documentação](../../../documentacao.md) > [Automacao](../../automacao.md) > [TerraForm](../terraform.md)

# Executando scripts do Terraform

Esta pagina contém o procedimento básico para executar os scripts de autormação do Terraform na AWS. Consulte a lista completa de comandos em <https://www.terraform.io/docs/commands/index.html>

1. No seu computador, abra um terminal (no Windows é o Command Prompt - CMD), vá para o diretório onde está o arquivo .bat\* com as chaves de acesso ao AWS, e execute-o (é só digitar o nome do arquivo bat)  
   \* em ambiente de dev o arquivo é o "set\_terraform\_cloudera\_dev.bat", se não tiver esse arquivo, fale com a gente (Bruno, Evandro, Cleide)  
     
   Este procedimento irá setar as chaves como variéveis na sua sessão ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)
2. Agora abra o diretório terraform do seu projeto (onde estão os scripts .tf) e execute o comando abaixo para iniciar o Terraform:

   **Aqui você está iniciando o Terraform com as variáveis de sessão do item 1**

   ```bash
   terraform init -backend-config "access_key=%AWS_ACCESS_KEY_ID%" -backend-config "secret_key=%AWS_SECRET_ACCESS_KEY%"
   ```
3. O comando "plan" vai analisar todos os scripts do seu diretório, e exibir na tela as alterações que serão aplicadas

   **Executando o script**

   ```bash
   terraform plan  -var ssh_private_key_file=%AWS_SSH_PRIVATE_KEY% -var aws_region=%AWS_DEFAULT_REGION%  -var path_keys_cloudera=%PATH_KEYS_CLOUDERA% -var rds_secret_string=%RDS_SECRET_STRING%


   ```
4. Após conferir as alterações exibidas no plan, execute o comando abaixo para que o Terraform aplique o script

   **Aplicando as alterações do script**

   ```bash
   terraform apply  -var ssh_private_key_file=%AWS_SSH_PRIVATE_KEY% -var aws_region=%AWS_DEFAULT_REGION%  -var path_keys_cloudera=%PATH_KEYS_CLOUDERA% -var rds_secret_string=%RDS_SECRET_STRING%


   ```
