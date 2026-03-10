
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [EMR](../../emr.md) > [Spark-EMR](../spark-emr.md)

# Codigo exemplo - pyspark

```py
import sys
from pyspark.sql import SparkSession

def create_spark_session(app_name):
    """
    Cria uma sessão spark
            Parametros:
                    app_name (str): Nome do job spark.
            Retorno:
                    spark_session (obj) : sessao spark
    """
    spark = SparkSession.builder \
            .appName(app_name) \
            .config("spark.sql.session.timeZone", "GMT") \
            .getOrCreate()
    return spark

def main():
    """
    Função principal.
            Retorno: None
    """
    #<argumento1> = sys.argv[1]
    #<argumento2> = sys.argv[2]

    spark = create_spark_session("exemplo-pyspark")
    spark.sparkContext.setLogLevel('WARN')

    #Seu código ETL
    print("Hello World")
               
    return None
if __name__ == '__main__':
    main()
```
