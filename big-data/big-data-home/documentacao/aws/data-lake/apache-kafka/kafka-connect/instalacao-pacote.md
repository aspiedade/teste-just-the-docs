
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Kafka](../../apache-kafka.md) > [Kafka-Connect](../kafka-connect.md)

# Instalacao - pacote

# Configuração e instalação do Kafka-Connect

```bash
# usuário root
sudo su
yum update -y
yum install java-1.8.0-openjdk -y

# Criando o usuário cp-kafka-connect
groupadd -r confluent
useradd cp-kafka-connect -G confluent

su cp-kafka-connect
cd ~/

# baixando e descompactando o pacote confluent
curl -O http://packages.confluent.io/archive/5.3/confluent-5.3.1-2.12.tar.gz
tar xzf confluent-5.3.1-2.12.tar.gz

cd confluent-5.3.1/

#Adicionando configurações no arquivo connect-avro-distributed.properties

vi etc/schema-registry/connect-avro-distributed.properties 

bootstrap.servers=<Lista de brokers>
key.converter=io.confluent.connect.avro.AvroConverter
key.converter.schema.registry.url=http://<hostname>:8081
value.converter=io.confluent.connect.avro.AvroConverter
value.converter.schema.registry.url=http://<hostname>:8081


#configurando as credenciais para acessar o S3
aws configure 




```

Expandir origem

```bash
AWS Access Key ID : <Sua Access Key>
AWS Secret Access Key : <Sua Secret Access Key>
Default region name: us-east-1
Default output format: text
```

```bash
#comando para voltar para o usuario root
exit 
```

```bash
#Criando um arquivo de serviço para o Kafka-Connect

touch /etc/systemd/system/kafka-connect.service
vi /etc/systemd/system/kafka-connect.service
```

**kafka-connect.service** Expandir origem

```text
[Unit]
Description=Confluent Kafka Connect
After=syslog.target
After=network.target

[Service]
WorkingDirectory=/home/cp-kafka-connect/confluent-5.3.1
ExecStart=/home/cp-kafka-connect/confluent-5.3.1/bin/connect-distributed /home/cp-kafka-connect/confluent-5.3.1/etc/schema-registry/connect-avro-distributed.properties
User=cp-kafka-connect
Type=simple
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=kafka-connect
SuccessExitStatus=143
TimeoutStopSec=120
Restart=always
```

# Criação do syslog conf

```bash
#Arquivo syslog conf para garantir que o systemd possa manipular os logs corretamente.
touch /etc/rsyslog.d/kafka-connect.conf
vi /etc/rsyslog.d/kafka-connect.conf
```

**kafka-connect.conf** Expandir origem

```text
if $programname == 'kafka-connect' then /var/log/kafka-connect.log
& stop
```

```bash
#Restart do syslog para carregar a nova configuração
systemctl restart rsyslog.service
```

```bash
#registrando o serviço no systemd para que ele seja iniciado com o sistema.
systemctl daemon-reload
systemctl start kafka-connect.service
systemctl status kafka-connect.service -l
systemctl enable kafka-connect.service

systemctl start kafka-connect.service
systemctl stop kafka-connect.service
systemctl restart kafka-connect.service 
```

Admin Guide: <https://docs.confluent.io/current/installation/installing_cp/zip-tar.html#prod-kafka-cli-install>
