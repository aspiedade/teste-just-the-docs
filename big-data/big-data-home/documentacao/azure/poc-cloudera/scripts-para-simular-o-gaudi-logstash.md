
[Documentação](../../../documentacao.md) > [Azure](../../azure.md) > [POC Cloudera](../poc-cloudera.md)

# Scripts para simular o Gaudi - Logstash

O objetivo dos scripts durante a POC : simular a escrita dos logs de autenticação e autorização, que em produção é feita pelo Gaudi, para que o logstash pudesse enviar as mensagens dos arquivos em questão para o kafka.

Script de autenticação

```py
import re
import json
import os
import gzip
import sys
from multiprocessing import Process
from multiprocessing import Pool
import time


from datetime import datetime

def processaLogs():
    vCont=0
    vContTotal=0
    tam=0
    vAverage={}
    minuto="00"
    minutoAux="00"
    hora ="16"
    horaAux=""

    dir_input = "/var/poc_cloudera/arquivos_processados/autenticacao_processada_02_ordenado.txt"
    dir_output = "/var/poc_cloudera/input_logstash/autenticacao/autenticacao_logstash.txt"
    print(datetime.now())
    w = open(dir_output, 'w')
    with open(dir_input, 'r') as r:
        for line in r:
            vLinha = line            
            horaAux = vLinha[21:23] 

            if horaAux==hora:                    
                vCont=vCont+1
                w.write(vLinha )  
                minuto = vLinha[24:26]
                '''
                tam=len(vLinha)
                if tam in vAverage:
                    vAverage[tam]+=1
                else:
                    vAverage[tam]=1
                '''
                #bLinha = r.readline()
                #vLinha = bLinha.decode('cp1252')
                
                #se o minuto mudar, espera 60s para continuar o script
                '''
                if vCont>1000000 :
                    vCont=0
                    input("Pressione alguma tecla para continuar")
                '''     
                if minutoAux!=minuto:
                    print(minutoAux +" - "+dir_input +"   :   "+ str(vCont))
                    minutoAux=minuto
                    vContTotal+=vCont
                    #break
                    time.sleep( 60 )
                    vCont=0          
    w.close()
    print(dir_input +" Total  :   "+ str(vContTotal))
   
processaLogs()



```

Script de autorização

```py
import re
import json
import os
import gzip
import sys
from multiprocessing import Process
from multiprocessing import Pool
import time


from datetime import datetime

def processaLogs():
    vCont=0
    vContTotal=0
    tam=0
    vAverage={}
    minuto="00"
    minutoAux="00"
    hora ="16"
    horaAux=""

    dir_input = "/var/poc_cloudera/arquivos_processados/autorizacao_processada_02_ordenado.txt"
    dir_output = "/var/poc_cloudera/input_logstash/autorizacao/autorizacao_logstash.txt"
    print(datetime.now())
    w = open(dir_output, 'w')
    with open(dir_input, 'r') as r:
        for line in r:
            vLinha = line            
            horaAux = vLinha[21:23] 

            if horaAux==hora:                    
                vCont=vCont+1
                w.write(vLinha )  
                minuto = vLinha[24:26]
                '''
                tam=len(vLinha)
                if tam in vAverage:
                    vAverage[tam]+=1
                else:
                    vAverage[tam]=1
                '''
                #bLinha = r.readline()
                #vLinha = bLinha.decode('cp1252')
                
                #se o minuto mudar, espera 60s para continuar o script
                '''
                if vCont>1000000 :
                    vCont=0
                    input("Pressione alguma tecla para continuar")
                '''     
                if minutoAux!=minuto:
                    print(minutoAux +" - "+dir_input +"   :   "+ str(vCont))
                    minutoAux=minuto
                    vContTotal+=vCont
                    #break
                    time.sleep( 60 )
                    vCont=0          
    w.close()
    print(dir_input +" Total  :   "+ str(vContTotal))
   
processaLogs()
```
