
[Documentação](../../../../../../documentacao.md) > [AWS](../../../../../aws.md) > [Data Lake](../../../../data-lake.md) > [Apache Kafka](../../../apache-kafka.md) > [Kafka-Connect](../../kafka-connect.md) > [Conectores](../conectores.md)

# Sink-S3

**Requisitos para criação do sink**:

- Cluster kafka;;
- Cluster zookeeper;
- Schema registry;
- Tópico criado.

**Avro**

```bash
curl -X POST \
http://<DNS do kafka connect>:8083/connectors \
-H "Content-Type: application/json" -d @- \
 << EOF
{
"name": "<nome do job>",
  "config": {
    "connector.class": "io.confluent.connect.s3.S3SinkConnector",
    "tasks.max": "2",
    "topics": "<Tópicos>",
    "s3.region": "us-east-1",
    "s3.bucket.name": "<Nome do bucket>",
    "topics.dir":"kafka",
    "s3.part.size": "5242880",
    "flush.size": "3",
    "storage.class": "io.confluent.connect.s3.storage.S3Storage",
    "format.class": "io.confluent.connect.s3.format.avro.AvroFormat",
    "schema.compatibility": "NONE",
    "key.converter.schemas.enable":"false",
    "value.converter.schemas.enable":"false",
    "rotate.schedule.interval.ms":"60000",
	"partition.duration.ms":"60000",
	"partitioner.class":"io.confluent.connect.storage.partitioner.TimeBasedPartitioner",
	"path.format":"'year'=YYYY/'month'=MM/'day'=dd/'hour'=HH",                                             
    "timestamp.extractor" : "Record",
    "timezone":"America/Sao_Paulo",
    "locale":"en-US",
    "avro.codec":"snappy"
  	}
}
EOF
```

**Json**

```bash
curl -X POST \
http://<DNS do kafka connect>:8083/connectors \
-H "Content-Type: application/json" -d @- \
 << EOF
{
"name": "<nome do job>",
  "config": {
    "connector.class": "io.confluent.connect.s3.S3SinkConnector",
    "tasks.max": "2",
    "topics": "<tópicos>",
    "s3.region": "us-east-1",
    "s3.bucket.name": "<Nome do bucket>",
    "topics.dir":"kafka",
    "s3.part.size": "5242880",
    "flush.size": "10000",
    "storage.class": "io.confluent.connect.s3.storage.S3Storage",
    "format.class": "io.confluent.connect.s3.format.json.JsonFormat",
    "schema.compatibility": "NONE",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable":"false",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable":"false",
	"rotate.schedule.interval.ms":"60000",
	"partition.duration.ms":"60000",
	"partitioner.class":"io.confluent.connect.storage.partitioner.TimeBasedPartitioner",
	"path.format":"'year'=YYYY/'month'=MM/'day'=dd/'hour'=HH",                                             
    "timestamp.extractor" : "Record",
    "timezone":"America/Sao_Paulo",
    "locale":"en-US"
	}
}
EOF
```
