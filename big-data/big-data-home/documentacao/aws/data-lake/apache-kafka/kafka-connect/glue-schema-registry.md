
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Kafka](../../apache-kafka.md) > [Kafka-Connect](../kafka-connect.md)

# Glue Schema Registry

# - [](#key-) - [Schema Registry](#schema-registry) - [Data Catalog](#data-catalog) - [MSK](#msk) - [Kafka Connect](#kafka-connect)

# Schema Registry

- Registra os schemas
- Limite de 1000 versões de schema por registry e região

# Data Catalog

- É possível criar uma tabela vinculando ao schema do registry

# MSK

Criação do topico de teste:

```bash
# Dentro da máquina msk-jump em qa
./kafka-topics.sh --create --zookeeper "$zookeeper_nodes" --replication-factor "2" --partitions "2" --topic "caribe.testes.schema.registry2" --config cleanup.policy=delete
```

Publicar mensagem em Avro:

```java
import org.apache.kafka.clients.producer.*;
import com.amazonaws.services.schemaregistry.serializers.avro.AWSKafkaAvroSerializer;
import com.amazonaws.services.schemaregistry.utils.AWSSchemaRegistryConstants;
import org.apache.kafka.common.errors.SerializationException;
import org.apache.avro.generic.GenericData;
import org.apache.avro.generic.GenericRecord;
import org.apache.avro.Schema;
import org.apache.avro.Schema.Parser;
import software.amazon.awssdk.services.glue.model.Compatibility;

import java.util.Properties;
import java.io.IOException;
import java.io.File;

public class MskTest {
    private static final Properties properties = new Properties();
    private static final String topic = "caribe.testes.schema.registry2";
    public static void main(final String[] args) throws IOException {
// Set the default synchronous HTTP client to UrlConnectionHttpClient
        System.setProperty("software.amazon.awssdk.http.service.impl", "software.amazon.awssdk.http.urlconnection.UrlConnectionSdkHttpService");
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "b-1.mskafka.zgflsq.c7.kafka.us-east-1.amazonaws.com:9092,b-2.mskafka.zgflsq.c7.kafka.us-east-1.amazonaws.com:9092");
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, AWSKafkaAvroSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, AWSKafkaAvroSerializer.class.getName());
        properties.put(AWSSchemaRegistryConstants.AWS_REGION, "us-east-1");
        properties.put(AWSSchemaRegistryConstants.REGISTRY_NAME, "caribe-qa");
        properties.put(AWSSchemaRegistryConstants.SCHEMA_NAME, "teste_user");
        properties.put(AWSSchemaRegistryConstants.COMPATIBILITY_SETTING, Compatibility.BACKWARD);
//        properties.put(AWSSchemaRegistryConstants.SCHEMA_AUTO_REGISTRATION_SETTING, true);
        Schema schema_customer = new Parser().parse(new File("bt-event-handler/src/main/resources/teste_user.avsc"));
        GenericRecord customer = new GenericData.Record(schema_customer);

        try (KafkaProducer<String, GenericRecord> producer = new KafkaProducer<String, GenericRecord>(properties)) {
            final ProducerRecord<String, GenericRecord> record = new ProducerRecord<String, GenericRecord>(topic, customer);
            customer.put("nam_login", "damiao2");
            customer.put("idt_person", 1234);
            customer.put("email", "teste2@uol.com.br");
            producer.send(record, new Callback() {
                @Override
                public void onCompletion(RecordMetadata metadata, Exception exception) {
                    System.out.println("ACK");
                }
            });
            System.out.println("Sent message");
            Thread.sleep(1000L);
        } catch (final InterruptedException | SerializationException e) {
            e.printStackTrace();
        }
    }
}
```

teste\_user.avsc

```java
{
  "type": "record",
  "namespace": "Caribe",
  "name": "Teste_User",
  "fields": [
    {
      "name": "nam_login",
      "type": "string"
    },
    {
      "name": "idt_person",
      "type": "int"
    },
    {
      "name": "email",
      "type": [
        "string",
        "null"
      ],
      "default": "null"
    }
  ]
}
```

# Kafka Connect

Criar sink para o S3 (não testado ainda)

```bash
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d @- << EOF
{
"name":"teste-schema-registry-s3-sink",
"config":
  {
    "connector.class":"io.confluent.connect.s3.S3SinkConnector",
    "topics.dir":"kafka/teste/schema-registry",
    "flush.size":"1",
    "s3.part.size":"26214400",
    "tasks.max":"2",
    "timezone":"America/Sao_Paulo",
    "locale":"en-US",
    "format.class":"io.confluent.connect.s3.format.avro.AvroFormat",
    "value.converter":"io.confluent.connect.avro.AvroConverter",
    "key.converter":"io.confluent.connect.avro.AvroConverter",
    "s3.region":"us-east-1",
    "s3.bucket.name":"uol-datalake-raw-qa",
    "s3.compression.type":"gzip",
    "partition.duration.ms":"1800000",
    "schema.compatibility":"NONE",
    "topics":"batepapo.presence.log",
    "key.converter.schemas.enable":"false",
    "partitioner.class":"io.confluent.connect.storage.partitioner.TimeBasedPartitioner",
    "value.converter.schemas.enable":"false",
    "name":"teste-schema-registry-s3-sink",
    "errors.tolerance":"all",
    "storage.class":"io.confluent.connect.s3.storage.S3Storage",
    "rotate.schedule.interval.ms":"1800000",
    "timestamp.extractor":"Record",
    "path.format":"'data'=YYYY-MM-dd",
    "key.converter.registry.name": "caribe-qa",
    "key.converter.dataFormat": "AVRO", 
    "value.converter.registry.name": "caribe-qa",
    "key.converter.schemaName":"teste_user",
    "value.converter.schemaName":"teste_user"
  }
}
EOF
```
