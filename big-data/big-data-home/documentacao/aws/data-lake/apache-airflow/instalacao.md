
[Documentação](../../../../documentacao.md) > [AWS](../../../aws.md) > [Data Lake](../../data-lake.md) > [Apache Airflow](../apache-airflow.md)

# Instalacao

**Pré-requisitos**:

- RDS Mysql
- Database airflow
- usuário airflow com grant all no database acima.

```sql
#Acesse o RDS que foi definido pro airflow e execute os seguintes comandos com o usuário root:

create database airflow DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;

grant all on airflow.* TO 'airflow'@'%' IDENTIFIED BY 'DEFINIR A SENHA';
```

```bash
#agora na ec2, com o usuário root, exeute os seguintes comandos:
sudo su -

sudo mkfs.ext4 /dev/nvme1n1
sleep 10 
sudo mkdir /log
sudo echo "/dev/nvme1n1 /log ext4 defaults 0 2" >> /etc/fstab
sudo mount -a

sudo yum update -y
#ajustar o timezone
sudo yum update tzdata.noarch
sudo rm -f /etc/localtime
sudo ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

#adicionar o usuario de sistema do airflow
sudo useradd airflow
usermod -aG wheel airflow
visudo 
%wheel ALL=(ALL)       NOPASSWD: ALL


export AIRFLOW_HOME=/etc/airflow

#criar os diretorios do airflow
sudo mkdir -p $AIRFLOW_HOME/dags
sudo mkdir $AIRFLOW_HOME/certs
sudo mkdir $AIRFLOW_HOME/plugins
sudo mkdir -p /log/airflow/webserver/
#sudo mkdir -p /log/old_logs/airflow/
sudo mkdir $AIRFLOW_HOME/defaults
sudo mkdir $AIRFLOW_HOME/scripts
sudo mkdir $AIRFLOW_HOME/variables

#instalar o mysql client
sudo yum -y install http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
  
#sudo yum update -y
sudo yum install -y python3
sudo yum install -y python3-libs
sudo yum install -y python3-devel
sudo yum install -y python3-pip
sudo yum install -y python3-setuptools
sudo yum install -y gcc gcc-c++
sudo yum install -y  mysql-community-devel
sudo yum install -y curl
sudo yum install -y wget
sudo yum install -y telnet





```

```bash
su - airflow


#definir as variaveis de ambiente
export PASSW_AIRFLOW=#DEFINIR
export SLUGIFY_USES_TEXT_UNIDECODE=yes
export AIRFLOW_HOME=/etc/airflow
export RDS_ENDPOINT=#DEFINIR
export AIRFLOW__CORE__SQL_ALCHEMY_CONN=mysql://airflow:$PASSW_AIRFLOW@$RDS_ENDPOINT:3306/airflow
export AIRFLOW__CORE__FERNET_KEY=smAeKLWkl-mJVD93kZXKLFxmAZfdsPgUBDiGtnGJgDc=

#instalar o airflow
cd $AIRFLOW_HOME
sudo pip3 install apache-airflow[crypto,devel,jdbc,ldap,mysql,s3,ssh,slack,postgres]

#sudo pip3 install airflow-prometheus-exporter
#sudo  pip3 install prometheus_client
#sudo pip3 install airflow-exporter

sudo vi $AIRFLOW_HOME/defaults/config
```

Expandir origem

```bash
PASSW_AIRFLOW=#DEFINIR
RDS_ENDPOINT=#DEFINIR
AIRFLOW_CONFIG=/etc/airflow/airflow.cfg
AIRFLOW_HOME=/etc/airflow/
AIRFLOW__CORE__DEFAULT_TIMEZONE=America/Sao_Paulo
AIRFLOW__CORE__SQL_ALCHEMY_CONN=mysql://airflow:$PASSW_AIRFLOW@$RDS_ENDPOINT:3306/airflow
AIRFLOW__CORE__FERNET_KEY=smAeKLWkl-mJVD93kZXKLFxmAZfdsPgUBDiGtnGJgDc=
AIRFLOW__WEBSERVER__RBAC=true
```

```bash
#criando o serviço airflow-webserver
sudo vi /usr/lib/systemd/system/airflow-webserver.service
```

Expandir origem

```bash
[Unit]
Description=Apache Airflow WebServer
Documentation=https://airflow.apache.org/index.html
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=airflow
Group=airflow
ExecStart=/usr/local/bin/airflow webserver
EnvironmentFile=/etc/airflow/defaults/config
TimeoutStopSec=180
Restart=always
 
[Install]
WantedBy=multi-user.target
```

```bash
#criando o serviço airflow-scheduler
sudo vi /usr/lib/systemd/system/airflow-scheduler.service
```

Expandir origem

```bash
[Unit]
Description=Apache Airflow Scheduler
Documentation=https://airflow.apache.org/index.html
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=airflow
Group=airflow
ExecStart=/usr/local/bin/airflow scheduler
EnvironmentFile=/etc/airflow/defaults/config
TimeoutStopSec=180
Restart=always
 
[Install]
WantedBy=multi-user.target
```

```bash
#criando arquivos para copiar dags do S3 para a máquina do airflow (DEFINA O AMBIENTE)
sudo vi /etc/airflow/scripts/copy-dags.sh
```

Expandir origem

```bash
#!/bin/bash
ENV=#DEFINIR O AMBIENTE
BUCKET=uol-datalake-artifacts-$ENV
sudo aws s3 sync s3://$BUCKET/airflow/dags/ /etc/airflow/dags/
sudo chown airflow. -R /etc/airflow/dags/
```

```bash
#criando arquivos para copiar plugins do S3 para a máquina do airflow (DEFINA O AMBIENTE)
sudo vi /etc/airflow/scripts/copy-plugins.sh
```

Expandir origem

```bash
#!/bin/bash
ENV=#DEFINIR O AMBIENTE
BUCKET=uol-datalake-artifacts-$ENV
sudo aws s3 sync s3://$BUCKET/airflow/plugins/ /etc/airflow/plugins/
sudo chown airflow. -R /etc/airflow/plugins/
```

```bash
#permissão de execução
sudo chmod +x /etc/airflow/scripts/*.sh
```

```bash
#criando logrotate
sudo vi /etc/logrotate.d/airflow 
```

Expandir origem

```bash
/log/airflow/*/*.log /log/airflow/*/*/*.log /log/airflow/*/*/*/*.log {
        daily
        rotate 5
        copytruncate
        compress
        su airflow airflow
        size 5M
}
```

```bash
#Criar arquivo de config do Airflow
sudo vi $AIRFLOW_HOME/airflow.cfg

#Copie o conteúdo do arquivo abaixo para o arquivo criado anteriormente:
https://stash.uol.intranet/projects/BIBD/repos/datalake/browse/terraform/ec2-airflow/files/airflow.cfg




```

```bash
#Iniciando database
sudo chown airflow. -R /etc/airflow/
sudo chown airflow. -R /log/airflow/
sudo chown airflow. -R /log/airflow/*
#sudo chown airflow. -R /log/old_logs/airflow/
sudo chmod 600 /etc/airflow/airflow.cfg


airflow initdb

sudo systemctl daemon-reload


#habilitando os serviços airflow-webserver e airflow-scheduler
sudo systemctl start airflow-webserver
sudo systemctl status airflow-webserver -l
sudo systemctl enable airflow-webserver

sudo systemctl start airflow-scheduler
sudo systemctl status airflow-scheduler -l
sudo systemctl enable airflow-scheduler

#redirecionamento da 80 para 8080. Com isso nao é necessário especificar a porta ao fazer um get.
sudo iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080

#crontab p/ executar os scripts
sudo vi /etc/crontab 
```

Expandir origem

```bash
*/5 * * * * root /etc/airflow/scripts/copy-dags.sh
*/5 * * * * root /etc/airflow/scripts/copy-plugins.sh
5 * * * * root /usr/sbin/logrotate -f /etc/logrotate.d/airflow
```

```bash
#criar variaveis
airflow variables --set env qa
airflow variables --set datalake_config_qa '{"aws_account":"240616793875", "aws_region":"us-east-1", "emr_spark_master":"emr-datalake-master-qa", "iam_role_redshift": "arn:aws:iam::240616793875:role/AWSRedshiftServiceRole"}'

#criar usuario admin
airflow create_user -r Admin -u bigdata_admin -e bigdata_admin@uolinc.com -f bigdata_admin -l bigdata_admin  -p ${definir senha}


```

Após isso, o Airflow estará disponível na porta 8080.

Referências:

<https://airflow.apache.org/>

<https://stash.uol.intranet/projects/AL/repos/aws-infra-airflow/browse>
