
[Documentação](../../../documentacao.md) > [How-to](../../how-to.md) > [GCP](../gcp.md)

# GCP - Boas praticas de autenticacao de aplicacao

- [Resumo](#resumo)
- [Execução local](#execu-o-local)
- [Deploy em GCP](#deploy-em-gcp)
- [Deploy em AWS](#deploy-em-aws)

# **Resumo**

Para execução local ou em GCP é recomendado o método de autenticação ADC (application default credentials): <https://cloud.google.com/docs/authentication/client-libraries>

# **Execução local**

1. Instalar gcloud SDK: <https://cloud.google.com/sdk/docs/install?hl=pt-br>  

2. Executar o comando no Terminal:

<https://cloud.google.com/sdk/gcloud/reference/auth/application-default/login>

```bash
gcloud auth application-default login 
```

Após rodar, será gerado um arquivo JSON com as credenciais do seu usuário e as bibliotecas do GCP em python conseguem identificar ela automaticamente.

(Opcional) Adicionar a variável de ambiente **GOOGLE\_CLOUD\_PROJECT** com o id do projeto usado para consumir os recursos. Caso não seja configurada essa variável, será o utilizado o projeto padrão selecionado durante a autenticação.

3. Instanciar as bibliotecas sem passar credenciais. Exemplos:

```py
from google.cloud import bigquery
client = bigquery.Client()

result = client.query("SELECT CURRENT_DATE()")
```

Exemplo de uso via Pandas:

```py
# Libs necessárias para acessar GCS pelo pandas
# pip install fsspec gcsfs

import pandas
pandas.read_csv("gs://path/arquivo.csv")
```

# **Deploy em GCP**

1. Instanciar as bibliotecas sem passar credenciais.

```py
from google.cloud import bigquery
client = bigquery.Client()
```

Ao rodar no GCP, o ambiente já está configurado com uma conta de serviço configurada no momento do deploy. Se não foi configurada nenhuma, ele assume a conta de serviço padrão do projeto.

# **Deploy em AWS**

1. Criar uma conta de serviço no GCP e gerar a chave JSON.

2. Adicionar conteúdo do arquivo JSON no Secrets Manager da AWS.

3. Na aplicação, ler o conteúdo do JSON e passar na criação do client. Exemplo:

```py
import boto3
from google.oauth2 import service_account
from google.cloud import bigquery

def get_secret_value(secret_name):
	region_name = "us-east-1"
	session = boto3.session.Session()

	client = session.client(
   		service_name='secretsmanager',
   		region_name=region_name
	)

	response = client.get_secret_value(
    	SecretId=secret_name
	)
	return response['SecretString'].encode("utf-8")

service_account_info = json.load(get_secret_value('minha_chave'))
credentials = service_account.Credentials.from_service_account_info(service_account_info)
client = bigquery.Client(credentials=credentials)
```
