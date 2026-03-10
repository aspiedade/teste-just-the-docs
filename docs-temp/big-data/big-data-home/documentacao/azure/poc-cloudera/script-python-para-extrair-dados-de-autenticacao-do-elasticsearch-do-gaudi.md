
[Documentação](../../../documentacao.md) > [Azure](../../azure.md) > [POC Cloudera](../poc-cloudera.md)

# Script Python para extrair dados de autenticacao do ElasticSearch do Gaudi

**Antes de tudo, acesse esta URL <https://kibana.gaudi.intranet>, e certifique-se de que tem ACL e seu usuário está autorizado a consultar os dados do Gaudi ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)**

Instale os pacotes conforme descrito nesta página: **https://github.com/taraslayshchuk/es2csv**

**Instação**

```bash
#Perceba que deve-se utilizar o Python na versão 2.7, como eu estava na versão 3.4, precisei instalar essa versão anterior (que não vai competir com a atual).
#na hora de instalar o pacote, aponte para a pip2 pra usar o python27 que voce acabou de instalar
pip2 install git+https://github.com/taraslayshchuk/es2csv.git
pip2 install es2csv
```

**Usage**

```bash
$ es2csv [-h] -q QUERY [-u URL] [-a AUTH] [-i INDEX [INDEX ...]]
         [-D DOC_TYPE [DOC_TYPE ...]] [-t TAGS [TAGS ...]] -o FILE
         [-f FIELDS [FIELDS ...]] [-S FIELDS [FIELDS ...]] [-d DELIMITER]
         [-m INTEGER] [-s INTEGER] [-k] [-r] [-e] [--verify-certs]
         [--ca-certs CA_CERTS] [--client-cert CLIENT_CERT]
         [--client-key CLIENT_KEY] [-v] [--debug]

Arguments:
 -q, --query QUERY                        Query string in Lucene syntax.               [required]
 -o, --output-file FILE                   CSV file location.                           [required]
 -u, --url URL                            Elasticsearch host URL. Default is http://localhost:9200.
 -a, --auth                               Elasticsearch basic authentication in the form of username:password.
 -i, --index-prefixes INDEX [INDEX ...]   Index name prefix(es). Default is ['logstash-*'].
 -D, --doc-types DOC_TYPE [DOC_TYPE ...]  Document type(s).
 -t, --tags TAGS [TAGS ...]               Query tags.
 -f, --fields FIELDS [FIELDS ...]         List of selected fields in output. Default is ['_all'].
 -S, --sort FIELDS [FIELDS ...]           List of <field>:<direction> pairs to sort on. Default is [].
 -d, --delimiter DELIMITER                Delimiter to use in CSV file. Default is ",".
 -m, --max INTEGER                        Maximum number of results to return. Default is 0.
 -s, --scroll-size INTEGER                Scroll size for each batch of results. Default is 100.
 -k, --kibana-nested                      Format nested fields in Kibana style.
 -r, --raw-query                          Switch query format in the Query DSL.
 -e, --meta-fields                        Add meta-fields in output.
 --verify-certs                           Verify SSL certificates. Default is False.
 --ca-certs CA_CERTS                      Location of CA bundle.
 --client-cert CLIENT_CERT                Location of Client Auth cert.
 --client-key CLIENT_KEY                  Location of Client Cert Key.
 -v, --version                            Show version and exit.
 --debug                                  Debug mode on.
 -h, --help                               show this help message and exit


```

**Exemplo**

```bash
#pra não validar o SSL, rodar este comando antes:
PYTHONHTTPSVERIFY=0 

es2csv -a seuUsuarioAqui:suaSenhaAqui -u https://kibana.gaudi.intranet/elasticsearch -q '*' -d "|" -f "@timestamp,idtPerson,username,domain,status,service,product,gaudiService,geoip.ip,geoip.longitude,geoip.latitude,geoip.city_name,geoip.country_name,geoip.country_code2,deviceId" -i gaudi-2018-07-05 -o autenticacaoKibana20180705.csv
 
Onde: 
1) gaudi-2018-07-05 é o indice com os dados do dia 05/07/2018, NUNCA EXTRAIA DE TUDO, faça sempre a extração por dia :)
2) autenticacaoKibana20180705.csv é o nome do arquivo csv que vai ser gerado na sua maquina
3) -q '*' = tá pegando qualquer coisa, pois não estou filtrando por nada, e o parametro de query é obrigatório
4) -d "|" = tentei definir o "|" como separador de colunas, mas não funcionou...
5) seuUsuarioAqui:suaSenhaAqui = acho que está mais do que claro, mas substitua pelo seu user e senha de rede (ahuahuahauhaua)
 
```

**Tempo de execução**

```bash
Tempo para extração de dados referentes a autenticações de um dia (05/07/2018): 25 horas e 30 minutos
Obs: script executado na minha maquina direto na rede cabeada (via VPN demora mais), abaixo evidencia:
 
Run query [###################################################################################################] [156020677/156020677] [100%] [18:40:56] [Time: 18:40:56] [  2.3 Kidocs/s]
Write to csv [#################################################################################################] [156020677/156020677] [100%] [6:50:00] [Time: 6:50:00] [  6.2 Kilines/s]


```
