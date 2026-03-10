
[Documentação](../../documentacao.md) > [POC](../poc.md)

# Provisionamento da instancia DataSunrise

Pelo AWS MarketPlace podemos contratar o Datasunrise, que é um proxy para mascaramento de dados, por tempo de consumo ao invés de uma licença anual. (Se o produto atender as necessidades, podemos mudar para uma licença anual que ficaria mais em conta)

O link para essa ferramenta no MarketPlace da AWS é [https://aws.amazon.com/marketplace/pp/B07RQTYGXN](https://aws.amazon.com/marketplace/pp/B07RQTYGXN "Follow link")

Custo da licença do Sunrisepor Hora por Database (além disso tem o custo da instancia):

| Unit type                             | Cost/unit   |
|:--------------------------------------|:------------|
| Protected database instances per hour | $1.40       |

1. Antes de qualquer coisa, verifique se sua conta na AWS possui uma role com a politica "aws-marketplace:MeterUsage", pois a imagem da instancia requer essa role para enviar as métricas de uso para realizar a cobrança. No nosso caso, solicitamos a criação da role

   ```
   "MktPlaceMeterUsage-role"
   ```

   **MktPlaceMeterUsage-role**

   ```java
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Action": [
                   "aws-marketplace:MeterUsage"
               ],
               "Effect": "Allow",
               "Resource": "*"
           }
       ]
   }
   ```
2. Adicione essa role no instancia EC2 que será provisionada, utilizando a imagem indicada pelo fornecedor.

   **Script do Terraform**

   ```java
   A nossa instancia foi provisionada via Terraform, vide script no Stash:
   https://stash.uol.intranet/projects/BIBD/repos/datalake/browse/terraform/ec2-datasunrise


   Para acessar o painel do Sunrise: https://{IP ou DNS da maquina}:11000/
   Use login 'admin' and InstanceID as password
   ```
3. Após autenticar-se no DataSunrise, você precisa configurar a conexão com a base de dados que passará pelo mascaramento, no nosso caso o Redshift. Porém, o Sunrise não encontrava o Driver ODBC do Redshift (mesmo estando instalando), precisei  editar o arquivo "odbcinst.ini" direto no terminal do Linux, adicionando o caminho para o driver odbc do Redshift:

   **Terminal Linux**

   ```java
   odbc_config --odbcinstini
   --> /etc/odbcinst.ini


   vi /etc/odbcinst.ini
   --> sudo vi /etc/odbcinst.ini
    
   #foi adicionado o conteudo abaixo, que foi copiado do arquivo modelo de instalação (/opt/amazon/redshiftodbc/Setup/odbcinst.ini ):
   [ODBC Drivers]
   Amazon Redshift (x86)=Installed
   Amazon Redshift (x64)=Installed
   [Amazon Redshift (x86)]
   Description=Amazon Redshift ODBC Driver(32-bit)
   Driver=/opt/amazon/redshiftodbc/lib/32/libamazonredshiftodbc32.so
   [Amazon Redshift (x64)]
   Description=Amazon Redshift ODBC Driver(64-bit)
   Driver=/opt/amazon/redshiftodbc/lib/64/libamazonredshiftodbc64.so
   ```
4. Depois de configurado odbcinst.ini, consegui criar a conexão com o Redshift, mas usando a string customizada (que especifica o driver) oferecida no painel do redshift como "ODBC URL"

   **String de conexão ODBC exibida no painel do Redshift na AWS**

   ```java
   Driver={Amazon Redshift (x64)}; Server=end_point_do_seu_cluster; Database=nome_do_database; UID=nome_do_user; PWD=sua_senha; Port=5439
   ```
5. Para conectar no Datasunrise usando o SQLDeveloper, é só alterar o host da sua conexão JDBC que antes apontava para o Redshift, para o host do Datasunrise (se você ainda não tem a conexão, veja como fazer em [https://docs.aws.amazon.com/pt\_br/redshift/latest/mgmt/configure-jdbc-connection.html](redshift://olago-qa.crpquptohq6j.us-east-1.redshift.amazonaws.com:5439/datalake))

   **Exemplo de conexão sem SSL**

   ```java
   Se você ainda não tem SSL na máquina do DataSunrise, tem que desabilitar o SSL no Redshift e na string pra conexão funcionar, se quiser criar o SSL é só seguir o passo 6
   DE = jdbc:redshift://olago-qa.crpquptohq6j.us-east-1.redshift.amazonaws.com:5439/datalake
   PARA = jdbc:redshift://10.80.4.210:5439/datalake?ssl=false
   ```
6. Para criar o SSL na máquina do DataSunrise, siga o procedimento da pagina 20 do <https://www.datasunrise.com/documents/DataSunrise_Database_Security_Suite_User_Guide.pdf>

   Esse procedimento vai gerar os certificados abaixo:

   - datasunrise\_proxy.crt
   - datasunrise\_proxy.csr
   - datasunrise\_proxy.key
   - rootCA.key
   - rootCA.pem
   - rootCA.srl

   Agora você só precisa copiar o rootCA.pem para o seu client e utiliza-lo na conexão.

   **Exemplo de conexão com SSL**

   ```java
   Indique o caminho do rootCA.pem que foi gerado acima, passando como valor do parametro sslRootCert, assim:
   jdbc:[redshift://10.80.4.210:5439/datalake?ssl=true&sslRootCert=C]:\Users\cverni\Desktop\sqldeveloper\jdk\jre\certificado_sunrise\rootCA.pem

   fonte: https://docs.aws.amazon.com/pt_br/redshift/latest/mgmt/configure-jdbc-options.htmlhttps://docs.aws.amazon.com/pt_br/redshift/latest/mgmt/configure-jdbc-options.html
   ```
7. Para utilizar o certificado no Metabase, é preciso converter o pem pra o formato der ou crt, executando o seguinte comando:

   ```java
   keytool -import -keystore $JAVA_HOME/lib/security/cacerts -storepass changeit -file rootCA.der -alias redshift -noprompt
   ```

O provisionamento dessa instancia foi feito nesta história do Jira: <https://jira.intranet.uol.com.br/jira/browse/BD-516>
