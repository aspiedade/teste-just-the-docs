
[Documentação](../../../documentacao.md) > [How-to](../../how-to.md) > [GCP](../gcp.md)

# Ler arquivos do GCS pelo Pandas

Para ler arquivos pelo pandas diretamente do GCS, usando a autenticação do próprio usuário, sem necessidade de service account:

- Precisa ter acesso ao projeto do GCP que está o bucket

- Instalar o gsutil: [https://cloud.google.com/storage/docs/gsutil\_install](https://cloud.google.com/storage/docs/gsutil_install "https://cloud.google.com/storage/docs/gsutil_install")

- Rodar o comando para autenticar com sua conta uolinc:

```bash
gcloud auth application-default login
```

- Instalar as dependências do pandas:

```bash
pip install fsspec gcsfs
```

Depois disso é possível ler os arquivos diretamente do pandas:

```py
import pandas
pandas.read_csv("gs://uol-analytics-temp/batepapo/agg_users_features_files/agg_user_features_20211130_20211213_part_000000000000.csv.gz")
```
