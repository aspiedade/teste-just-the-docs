
[Documentação](../../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../../data-lake-gcp.md) > [Otimizacao de recursos](../../../otimizacao-de-recursos.md) > [Recorrencias](../../recorrencias.md) > [Processo de limpeza de tabelas inativas no BigQuery - purga](../processo-de-limpeza-de-tabelas-inativas-no-bigquery-purga.md)

# Execucoes da purga

## 23 de Fevereiro de 2025

Disparo aviso: **2026-02-02**

Deleção tabelas: **2026-02-23**

**Artefatos:**

- Planilha de controle:
  - Snapshot: [purga_mapeamento_tabelas_20-01-2026.xlsx](../../../../../../../attachments/544602149.xlsx)
  - Planilha online: [purga\_mapeamento\_tabelas\_20-01-2026.xlsx](https://uolinc.sharepoint.com/:x:/r/sites/UOLCSDatalakeFiles/Documentos%20Compartilhados/caribe/arquivos_justificativa/purga_mapeamento_tabelas_20-01-2026.xlsx?d=waa852e9dff7149d682d6795c5f167eb3&csf=1&web=1&e=RXMBjg)
- Pull request:
  - <https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/pull-requests/53>
- Jira:
  [[AP11CAR-1549] [Purga] Purga de tabelas - 2026](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-1549)

**Redução de Custos:**

| storage\_billing\_model   |   num\_tables |   long\_term\_logical\_gb |   active\_logical\_gb |   long\_term\_physical\_gb |   active\_physical\_gb |   fail\_safe\_physical\_gb | num\_total\_cost\_monthly   |
|:--------------------------|--------------:|--------------------------:|----------------------:|---------------------------:|-----------------------:|---------------------------:|:----------------------------|
| LOGICAL                   |            56 |                     72.4  |                     0 |                      14.92 |                      0 |                          0 | USD 0.72                    |
| PHYSICAL                  |           116 |                   2835.61 |                     0 |                     348.04 |                      0 |                          0 | USD 6.96                    |

## 05 de Maio de 2025

Disparo aviso: **2025-04-14**

Deleção tabelas: **2025-05-05**

**Artefatos:**

- Planilha de controle:
  - Snapshot: [purga_mapeamento_tabelas_14-04-2025.xlsx](../../../../../../../attachments/544572514.xlsx)
  - Planilha online: <https://uolinc.sharepoint.com/:x:/r/sites/UOLCSDatalakeFiles/_layouts/15/doc2.aspx?sourcedoc=%7B51641187-EEBD-4F59-A1D5-44CA9C4D01BE%7D&file=purga_mapeamento_tabelas_14-04-2025.xlsx&action=default&mobileredirect=true>
- Pull request:
  - <https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/pull-requests/15>
  - <https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/pull-requests/17>
  - <https://stash.uol.intranet/projects/BIBD/repos/datalake-sql/pull-requests/18>
- Jira: [AP11CAR-1200](https://jira.intranet.uol.com.br/jira/browse/AP11CAR-1200)

**Redução de Custos:**

| storage\_billing\_model   |   num\_tables | long\_term\_logical\_gb   | active\_logical\_gb   | long\_term\_physical\_gb   | active\_physical\_gb   | fail\_safe\_physical\_gb   | num\_total\_cost\_monthly   |
|:--------------------------|--------------:|:--------------------------|:----------------------|:---------------------------|:-----------------------|:---------------------------|:----------------------------|
| LOGICAL                   |            33 | 1,89                      | 0,0                   | 0,2                        | 0,0                    | 0,0                        | USD 0,02                    |
| PHYSICAL                  |            45 | 1548,89                   | 0,0                   | 130,6                      | 0,0                    | 0,0                        | USD 2,61                    |
