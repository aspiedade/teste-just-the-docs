
[Documentação](../../../../../../documentacao.md) > [AWS](../../../../../aws.md) > [Data Lake](../../../../data-lake.md) > [Apache Kafka](../../../apache-kafka.md) > [Kafka-Connect](../../kafka-connect.md) > [Conectores](../conectores.md)

# Consultar o status de um conector

```bash
curl -X GET http://<DNS do kafka connect>:8083/connectors/<nome de conector>/status 
```

## Consultar o JSON de configuração de um conector

```bash
curl -X GET http://<DNS do kafka connect>:8083/connectors/<nome de conector> 


#ex: curl -X GET http://localhost:8083/connectors/authentication-s3-sink
```

## Excluir um conector

```bash
curl -X DELETE http://<DNS do kafka connect>:8083/connectors/<nome de conector> 


#ex: curl -X DELETE http://localhost:8083/connectors/authentication-s3-sink
```
