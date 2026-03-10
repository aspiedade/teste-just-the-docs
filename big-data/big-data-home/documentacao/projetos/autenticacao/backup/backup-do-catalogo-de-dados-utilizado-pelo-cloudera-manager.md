
[Documentação](../../../../documentacao.md) > [Projetos](../../../projetos.md) > [Autenticacao](../../autenticacao.md) > [Backup](../backup.md)

# Backup do catalogo de dados utilizado pelo Cloudera Manager

Os metadados utilizados pelo Cloudera Manager e seus serviços são armazenados em uma instancia MySQL, instalada na master-node1, então para evitar a instalação de clients mysql em outro node do cluster e gerar trafego desnecessária dos arquivos, a rotina de backp do Mysql está na própria master.

Foi criado um script em bash para acessar o Mysql, identificar o nome dos BDs dos serviços utilizados pelo Cloudera Manager (tudo que não é gerencial do Mysql), extrair o dump desses BDs e enviar para o S3.

Passo a passo antes de montar o script bash:

1. Criar o usuário para essa rotina de BKP  no S.O. e no MySQL

   **No Linux**

   ```bash
   useradd svc_backupid
   ```

   **No MySQL**

   ```sql
   --criando o user com as permissoes necessarias pra fazer o dump
   GRANT LOCK TABLES, SELECT ON *.* TO 'svc_backup'@'%' IDENTIFIED BY '[ senha secreta aqui ]';
   flush privileges;
   ```
2. Ao conectar no Mysql ou mysqldump, precisamos passar o user e senha, e deixar essas informações plain disponível no script não é legal (nem seguro), então foi utilizado o **mysql\_config\_editor** para gerar um arquivo de configuração que armazena a senha criptografada ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)

   **Mysql Config Editor**

   ```bash
   #basta chamar mysql_config_editor direto pelo terminal do linux, passando os paramentos necessarios + o login-path, que será utilizado na autenticação do Mysql, desta forma: 
   mysql_config_editor set --login-path=mysqldump --host=localhost --user=svc_backup --password
   #depois que digitar Enter, será solicitada a digitação da senha (que não aparece na tela e não dá pra rastrear o historico hohoho)

   #com isso, ao inves de passar o user e senha (-usvc_backup -p) na autenticacao, e so indicar login-path:
   #ao invés de 
   mysqldump -usvc_backup -p[ senha secreta aqui ] amon > amon-backup.sql
   #fica 
   mysqldump --login-path=mysqldump amon > amon-backup.sql


   #dessa forma, posso criar um outro login-path com outros dados de autenticação, e só passar esse caminho
   mysql_config_editor set --login-path=cleideteste --host=localhost --user=cleide --password


   mysql --login-path=cleideteste 

   ```
3. Agora que já conseguimos extrair os dados do MySQL, precisamos saber como enviá-los para o S3, a melhor solução foi utilizar o AWS cli:

   **AWS cli**

   ```bash
   #Mas não tinha a AWS Cli na maquina, então tive que instalar:
   python3.7 -m pip install awscli --upgrade --user

   #Para upar os arquivos (do EC2 -> S3), o comando é esse aqui:
   aws s3 --region us-east-1 sync /home/svc_backup/backup s3://bigdata-backup-dev

   #Para fazer o download (do S3 -> EC2), é só inverter a ordem da origem e destino
   aws s3 --region us-east-1 sync s3://bigdata-backup-dev /home/svc_backup/backup

   #como estou com problemas com a var de ambiente do aws (e sem tempo de configurar), tem que chamar o binario passando o caminho completo
   /home/svc_backup/.local/bin/aws s3 --region us-east-1 sync /home/svc_backup/backup s3://bigdata-backup-dev

   ```

Tudo pronto, o script ficou assim:

**mysql-backup.sh**

```bash
#!/bin/bash

DB_BACKUP_PATH='/home/svc_backup/backup'
TODAY=`date +%Y-%m-%d`
mkdir -p ${DB_BACKUP_PATH}/${TODAY}

echo "Criando dir ${TODAY}"


mysql --login-path=mysqldump -A --skip-column-names -e"SELECT schema_name FROM information_schema.schemata WHERE schema_name NOT IN ('information_schema','mysql','performance_schema')" > ${DB_BACKUP_PATH}/ListOfDatabases.txt

for DB in `cat ${DB_BACKUP_PATH}/ListOfDatabases.txt`
do
    mysqldump --login-path=mysqldump --hex-blob --routines --triggers ${DB} | gzip > ${DB_BACKUP_PATH}/${TODAY}/${DB}.sql.gz &
done
wait


#enviando os arquivos do EC2 para S3
/home/svc_backup/.local/bin/aws s3 --region us-east-1 sync ${DB_BACKUP_PATH} s3://bigdata-backup-dev

##### Remove os arquivos de 3 dias atras  #####

DBDELDATE=`date +%Y-%m-%d --date="-3 day"`
echo "Removendo dir ${DBDELDATE}"

if [ ! -z ${DB_BACKUP_PATH} ]; then
      cd ${DB_BACKUP_PATH}
      if [ ! -z ${DBDELDATE} ] && [ -d ${DBDELDATE} ]; then
            rm -rf ${DBDELDATE}
      fi
fi


```

```
Agora que já temos o script bash criado, precisamos dar permissão de execução:
```

**Sem isso não vai...**

```bash
chmod +x mysql-backup.sh

```

Belezura, depois de testar o script (rodando manualmente N vezes), vamos agendar o crontab pra rodar o script de backup!

**crontab**

```bash
#adicionar esta linha, para executar o script diariamente as 18:00 e salvar os logs em no arquivo indicado
0 18 * * * /home/svc_backup/mysql-backup.sh  >> /home/svc_backup/log/mysql-backup.log 2>&1

```

Ótemo, só que do jeito que está, vai chegar uma hora que o arquivo de log (mysql-backup.log) vai ficar tão grande que não dará mais pra ler/escrever, então precisamos fazer o **logrotate**...

**Instalando o Logrotate**

```bash
yum update && yum install logrotate
```

Aí ele vai gerar um arquivo de configuração (simples) no /etc/logrotate.conf e um diretorio /etc/logrotate.d com alguns arquivos de conf mais especificos. A gente pode editar o logrotate.conf ou adicionar um arquivo de configuração especifico para a nossa necessidade, que é o que vamos fazer aqui ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)

```bash
#se quiser pode dar um ls /etc/logrotate.d pra ver o que tem nesse diretorio, mas não é necessário
ls /etc/logrotate.d
#o resultado aqui foi: chrony  haproxy  httpd  mysql  syslog  wpa_supplicant  yum

#vamos criar o nosso arquivo de configuração pro backup chamado mysql-backup, heheh
vi /etc/logrotate.d/mysql-backup


#e adicionar este bloco no arquivo, que vai considerar a periodicidade diaria, que vai gerar um arquivo de log com compressao (vai gerar um arquivo .gz) qdo chegar em 1Mb, e zerar o arquivo de log original, e manter apenas os 3 arquivos .gz mais recentes (o comando su é para permitir o root zerar o arquivo de log e criar os arquivos compactados):
/home/svc_backup/log/*.log {
        daily
        rotate 3
        copytruncate
        compress
        su svc_backup svc_backup
        size 1M
}




#para testar se funciona, vamos forçar (--force ou -f) a execução do logrotate no modo verbose pra detalhar a execução: 
logrotate --force --verbose /etc/logrotate.d/mysql-backup
```

Como o logrotate precisa rodar como root (preferencialmente) e o nosso script de backup como svc\_backup, agenda os jobs do cron pelo "/etc/crontab" pra ficar tudo num lugar só, especificando o usuario

```bash
vi /etc/crontab

#adicionar as linhas de agendamento do job e salvar 

SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed

0 18 * * * svc_backup /home/svc_backup/mysql-backup.sh  >> /home/svc_backup/log/mysql-backup.log 2>&1
5 18 * * * root /etc/logrotate -f /etc/logrotate.d/mysql-backup > /dev/null 2>&1
```

Links utilizados como referencia pra logrotate:  
<https://www.tecmint.com/install-logrotate-to-manage-log-rotation-in-linux/><http://www.dicas-l.com.br/arquivo/utilizando_logrotate.php#.XFsqk1xKi70>
