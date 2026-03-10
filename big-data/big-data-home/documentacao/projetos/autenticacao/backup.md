
[Documentação](../../../documentacao.md) > [Projetos](../../projetos.md) > [Autenticacao](../autenticacao.md)

# Backup

create database auth\_bkp location 's3a://bigdata-backup-dev/database/auth\_bkp';

CREATE external TABLE auth\_bkp.raw\_authentication\_bkp  
(  
idt\_person BIGINT,  
event\_date Timestamp,  
service Int,  
--partition\_range String,  
product Int,  
username String,  
application Int,  
ip\_username String,  
status Int,  
type\_user String,  
gaudi\_service String,  
ip\_client String,  
domain String,  
device\_id String,  
geoip\_city\_name String,  
geoip\_country\_name String,  
geoip\_continent\_code String,  
geoip\_latitude String,  
geoip\_longitude String,  
geoip\_postal\_code String,  
geoip\_region\_code String,  
geoip\_region\_name String,  
geoip\_timezone String,  
host String,  
port\_username String,  
reputation\_ip String,  
elapsedtime Int  
)  
partitioned by (partition\_range String)  
stored as PARQUET;

set COMPRESSION\_CODEC=gzip;

insert into auth\_bkp.raw\_authentication\_bkp PARTITION (partition\_range)  
select   
idt\_person ,  
event\_date ,  
service ,  
product ,  
username ,  
application ,  
ip\_username ,  
status ,  
type\_user ,  
gaudi\_service ,  
ip\_client ,  
domain ,  
device\_id ,  
geoip\_city\_name ,  
geoip\_country\_name ,  
geoip\_continent\_code ,  
geoip\_latitude ,  
geoip\_longitude ,  
geoip\_postal\_code ,  
geoip\_region\_code ,  
geoip\_region\_name ,  
geoip\_timezone ,  
host ,  
port\_username ,  
reputation\_ip ,  
elapsedtime,  
partition\_range  
--from\_timestamp(now(),'yyyy-MM-dd') as dat\_backup  
from auth\_adm.raw\_authentication a  
order by a.partition\_range;

```bash
 /bin/spark-submit --master yarn --deploy-mode cluster   \
 --driver-memory 1g   --num-executors 4 --executor-cores 1 --executor-memory 2g     
 --class br.com.uol.bigdata.spark.BackupKudu    \
 --conf spark.streaming.receiver.writeAheadLog.enable=false
 --conf spark.eventLog.enabled=false \
 --conf spark.sql.parquet.compression.codec=gzip \
 /home/svcacc_sparkjobs/backup-kudu.jar   \
 "BackupKudu"    \
 "master-node1-prd:7051,master-node2-prd:7051,master-node3-prd:7051"   \
 "impala::auth_adm.raw_authentication"   \
 "2019-05-16"    \
 "2019-05-17"   \
 "s3a://bigdata-backup-prd/database/auth_adm/table/raw_authentication/"
```
