
[Documentação](../../../../../documentacao.md) > [GCP - Google Cloud Platform](../../../../gcp-google-cloud-platform.md) > [Data Lake - GCP](../../../data-lake-gcp.md) > [Interno - Devs](../../interno-devs.md) > [Airflow Composer](../airflow-composer.md)

# Airflow Composer - Interface

- [Tela inicial](#tela-inicial)
- [Trigger Manual](#trigger-manual)
- [Visão "Tree"](#vis-o-tree)
- [Visão "Graph"](#vis-o-graph)
- [Verficar log de execuções](#verficar-log-de-execu-es)
- [Repetir uma task que falhou](#repetir-uma-task-que-falhou)
- [Forçar status da task](#for-ar-status-da-task)

## **Tela inicial**

**Versão atual**

Filtros na tela inicial antes:

![](../../../../../../attachments/501590611.png)

**Versão nova:**

Filtrar dags que estejam executando no momento

**![](../../../../../../attachments/501590612.png)**

## **Trigger Manual**

Versão atual:

![](../../../../../../attachments/501590808.png)

Para executar "com parâmetros" era necessário inserir manualmente a estrutura do json, passando os nomes das tabelas.

Versão nova:

![](../../../../../../attachments/501590822.png)

Todas as tabelas já aparecerão na interface. O json já estará montado.

Exemplo com múltiplas queries na mesma DAG:

![](../../../../../../attachments/501590820.png)

## **Visão "Tree"**

A visão em árvore foi **substituída** por uma visão chamada "Grid".

Versão atual

**![](../../../../../../attachments/501590471.png)**

Versão nova

**![](../../../../../../attachments/501590473.png)**

## **Visão "Graph"**

Versão atual:

![](../../../../../../attachments/501590639.png)

Versão nova:

![](../../../../../../attachments/501590605.png)

**Visão da DAG na vertical:**

![](../../../../../../attachments/501590603.png)

## **Verficar log de execuções**

Versão atual:

![](../../../../../../attachments/501590560.png)

Versão nova:

**![](../../../../../../attachments/501590474.png)![](../../../../../../attachments/501590475.png)**

## **Repetir uma task que falhou**

Versão atual:

![](../../../../../../attachments/501590640.png)

Versão nova:

![](../../../../../../attachments/501590478.png)

## **Forçar status da task**

Versão atual

![](../../../../../../attachments/501590556.png)

Versão nova:

**![](../../../../../../attachments/501590479.png)**
