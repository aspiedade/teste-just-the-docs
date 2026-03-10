
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Kafka](../../apache-kafka.md) > [Kafka-Connect](../kafka-connect.md)

# Instalacao via yum

# Configuração e instalação do Kafka-Connect

```bash
# usuário root
sudo su

sudo yum update -y
sudo yum install java-1.8.0-openjdk -y
sudo yum install curl which -y

sudo rpm --import https://packages.confluent.io/rpm/5.3/archive.key
sudo vi /etc/yum.repos.d/confluent.repo

```

Expandir origem

```bash
[Confluent.dist]
name=Confluent repository (dist)
baseurl=https://packages.confluent.io/rpm/5.3/7
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/5.3/archive.key
enabled=1

[Confluent]
name=Confluent repository
baseurl=https://packages.confluent.io/rpm/5.3
gpgcheck=1
gpgkey=https://packages.confluent.io/rpm/5.3/archive.key
enabled=1
```

```bash
sudo yum clean all && sudo yum install confluent-platform-2.12

confluent-hub install confluentinc/kafka-connect-s3:latest

```

Expandir origem

```java
The component can be installed in any of the following Confluent Platform installations:
  1. / (installed rpm/deb package)
  2. / (where this tool is installed)
Choose one of these to continue the installation (1-2): 1
Do you want to install this into /usr/share/confluent-hub-components? (yN) y


Component's license:
Confluent Community License
http://www.confluent.io/confluent-community-license
I agree to the software license agreement (yN) y


Downloading component Kafka Connect S3 5.3.1, provided by Confluent, Inc. from Confluent Hub and installing into /usr/share/confluent-hub-components
Detected Worker's configs:
  1. Standard: /etc/kafka/connect-distributed.properties
  2. Standard: /etc/kafka/connect-standalone.properties
  3. Standard: /etc/schema-registry/connect-avro-distributed.properties
  4. Standard: /etc/schema-registry/connect-avro-standalone.properties
Do you want to update all detected configs? (yN) y

```

```bash
vi etc/schema-registry/connect-avro-distributed.properties 
```

Expandir origem

```bash
# Sample configuration for a distributed Kafka Connect worker that uses Avro serialization and
# integrates the the Schema Registry. This sample configuration assumes a local installation of
# Confluent Platform with all services running on their default ports.

# Bootstrap Kafka servers. If multiple servers are specified, they should be comma-separated.
bootstrap.servers=<>:9092

# The group ID is a unique identifier for the set of workers that form a single Kafka Connect
# cluster
group.id=connect-cluster

# The converters specify the format of data in Kafka and how to translate it into Connect data.
# Every Connect user will need to configure these based on the format they want their data in
# when loaded from or stored into Kafka
key.converter=io.confluent.connect.avro.AvroConverter
key.converter.schema.registry.url=http://<>:8081
value.converter=io.confluent.connect.avro.AvroConverter
value.converter.schema.registry.url=http://<>:8081
key.converter.schemas.enable=false
value.converter.schemas.enable=false



# Internal Storage Topics.
#
# Kafka Connect distributed workers store the connector and task configurations, connector offsets,
# and connector statuses in three internal topics. These topics MUST be compacted.
# When the Kafka Connect distributed worker starts, it will check for these topics and attempt to create them
# as compacted topics if they don't yet exist, using the topic name, replication factor, and number of partitions
# as specified in these properties, and other topic-specific settings inherited from your brokers'
# auto-creation settings. If you need more control over these other topic-specific settings, you may want to
# manually create these topics before starting Kafka Connect distributed workers.
#
# The following properties set the names of these three internal topics for storing configs, offsets, and status.
config.storage.topic=connect-configs
offset.storage.topic=connect-offsets
status.storage.topic=connect-statuses

# The following properties set the replication factor for the three internal topics, defaulting to 3 for each
# and therefore requiring a minimum of 3 brokers in the cluster. Since we want the examples to run with
# only a single broker, we set the replication factor here to just 1. That's okay for the examples, but
# ALWAYS use a replication factor of AT LEAST 3 for production environments to reduce the risk of 
# losing connector offsets, configurations, and status.
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

# The config storage topic must have a single partition, and this cannot be changed via properties. 
# Offsets for all connectors and tasks are written quite frequently and therefore the offset topic
# should be highly partitioned; by default it is created with 25 partitions, but adjust accordingly
# with the number of connector tasks deployed to a distributed worker cluster. Kafka Connect records
# the status less frequently, and so by default the topic is created with 5 partitions.
#offset.storage.partitions=25
#status.storage.partitions=5

# The offsets, status, and configurations are written to the topics using converters specified through
# the following required properties. Most users will always want to use the JSON converter without schemas. 
# Offset and config data is never visible outside of Connect in this format.
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# Confluent Control Center Integration -- uncomment these lines to enable Kafka client interceptors
# that will report audit data that can be displayed and analyzed in Confluent Control Center
# producer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringProducerInterceptor
# consumer.interceptor.classes=io.confluent.monitoring.clients.interceptor.MonitoringConsumerInterceptor

# These are provided to inform the user about the presence of the REST host and port configs
# Hostname & Port for the REST API to listen on. If this is set, it will bind to the interface used to listen to requests.
#rest.host.name=0.0.0.0
#rest.port=8083

# The Hostname & Port that will be given out to other workers to connect to i.e. URLs that are routable from other servers.
#rest.advertised.host.name=0.0.0.0
#rest.advertised.port=8083

# Set to a list of filesystem paths separated by commas (,) to enable class loading isolation for plugins
# (connectors, converters, transformations). The list should consist of top level directories that include
# any combination of:
# a) directories immediately containing jars with plugins and their dependencies
# b) uber-jars with plugins and their dependencies
# c) directories immediately containing the package directory structure of classes of plugins and their dependencies
# Examples:
# plugin.path=/usr/local/share/java,/usr/local/share/kafka/plugins,/opt/connectors,
plugin.path=/usr/share/java,/usr/share/confluent-hub-components



```

```bash
vi /usr/lib/systemd/system/confluent-kafka-connect.service
```

**confluent-kafka-connect.service** Expandir origem

```text
[Unit]
Description=Apache Kafka Connect - distributed
Documentation=http://docs.confluent.io/
After=network.target confluent-kafka.target

[Service]
Type=simple
User=cp-kafka-connect
Group=confluent
ExecStart=/usr/bin/connect-distributed /etc/kafka/connect-distributed.properties
EnvironmentFile=/etc/default/kafka-connect
TimeoutStopSec=180
Restart=no

[Install]
WantedBy=multi-user.target
```

```bash
vi /etc/default/kafka-connect
```

Expandir origem

```bash
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
```

```bash
chown -R cp-kafka-connect:confluent /etc/default/kafka-connect
```

```bash
systemctl daemon-reload
```

```bash
systemctl daemon-reload
sudo systemctl start confluent-kafka-connect
sudo systemctl status confluent-kafka-connect -l
systemctl enable confluent-kafka-connect
```

Admin Guide: <https://docs.confluent.io/current/installation/installing_cp/zip-tar.html#prod-kafka-cli-install>
