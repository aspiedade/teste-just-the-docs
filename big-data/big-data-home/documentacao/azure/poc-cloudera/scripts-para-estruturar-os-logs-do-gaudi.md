
[Documentação](../../../documentacao.md) > [Azure](../../azure.md) > [POC Cloudera](../poc-cloudera.md)

# Scripts para estruturar os logs do Gaudi

Logs de autorização

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


def GetServiceAuthorization(vLinha, vdataAutorizacao):
    vMetodo ='GetServiceAuthorization'
    try:
        vet=re.search(r"(\[\{).*(\}\])",vLinha).group().replace('[{','').replace('}]','').split(', ')
        dicionario={'I':'','US':'','IS':''}
        for x in vet:
            vet2=x.split('=')
            dicionario[vet2[0]]=vet2[1]
        
        #segundo regex  [RET=10266544 1 0:9-1[53,53,53] ] from
        vet2=re.search(r"(\[RET\=).*(\])",vLinha).group().replace('[RET=','').replace(' ] from','').split(' ')
        dicionario['STATUS'] = int(vet2[1])
        
        if dicionario['STATUS'] < 800:
            dicionario['IS'] = dicionario['IS'].replace(',','')
            
            #set idt_person
            if len(dicionario['I']) == 0:
                dicionario['I'] = vet2[0]
            
            #j = json.dumps({'data': vetLinha[0]+' '+vetLinha[1],'person':idtPerson,'email':'', 'status': 1, 'servicos':listServico})

            #parsed_time = datetime.strptime(vdataAutorizacao, "%Y-%m-%d %H:%M:%S,%f")
        #   print(vLinha)
            dic_retorno={'data': vdataAutorizacao,
                        'person':int(dicionario['I']),
                        'email':dicionario['US'],
                        'status':dicionario['STATUS'],
                        'statusDescricao': '',
                        'servicos':[],
                        'metodo': vMetodo}
            vet_produtos=[]
            try:
                retorno_servico=''
                retorno_servico = int(re.search(r"([0-9]\:).*(\[)",vet2[2]).group().replace('0:'+ dicionario['IS'] + '-','').replace('[',''))
            #  dic_retorno['status_servico'] = retorno_servico
                
                try:
                    
                    vet_produtos = re.search(r"(\[).*(\])",vet2[2]).group().replace('[','').replace(']','').split(',')
                    vet_produtos = list(map(int, vet_produtos))
                except ValueError:
                    vet_produtos=[]

            except AttributeError:
                pass
            obj_servico= {'servico': int(dicionario['IS']),'status_servico':retorno_servico,'produtos':vet_produtos}
            #if len(vet_produtos) > 0:
            #   obj_servico['produtos'].append(vet_produtos)

            dic_retorno['servicos'].append(obj_servico)
            return json.dumps(dic_retorno)

    except:
        print(vLinha)
        dic_retorno=None
        return json.dumps(dic_retorno)

    

def GetUserInscriptions(vLinha, vdataAutorizacao):
    vMetodo ='GetUserInscriptions'
    vet=re.search(r"(\[\{).*(\}\] \[)",vLinha).group().replace('[{','').replace('}]','').replace(' [','').split(', ')
    dicionario={'I':'','US':'','IS':''}
    for x in vet:
        vet2=x.split('=')
        dicionario[vet2[0]]=vet2[1]


    dic_retorno={'data': vdataAutorizacao,
                'person':int(dicionario['I']),
                'email':dicionario['US'],
                'status':'',
                'statusDescricao': '',
                'servicos':[],
                'metodo':vMetodo}

    for linha_inscription in re.search(r"(\:\{).*(\}\] from)",vLinha).group().replace(':','').replace('] from','').split('}'):
        vet_inscription = linha_inscription.split('={')                    
        try:
            
            vet_inscription[0] = int(vet_inscription[0].replace('{','').replace(', ',''))
            idt_inscription=None
            for x in vet_inscription:
                if idt_inscription == None:
                    idt_inscription = x
                else:
                    for servicos in x.split('],'):
                        #retorno
                        try:
                            retorno_servico = int(re.search(r"(\-).*(\=\[)",servicos).group().replace('-','').replace('=[','')) 
                            
                            servico = int(re.search(r"([0-9]).*(\-)",servicos).group().replace('-','')) 
                            obj_servico= {'servico': servico,'status_servico':retorno_servico,'produtos':[]}

                            produtos = re.search(r"(\=\[).*",servicos).group().replace('=[','').replace(']','')
                            
                            if "," in produtos:
                                for produto in produtos.split(','):
                                    obj_servico['produtos'].append(int(produto))
                            else:
                                obj_servico['produtos'].append(int(produtos))
                            
                            dic_retorno['servicos'].append(obj_servico)            
                        except (AttributeError, ValueError) as e:
                            pass
            
        except ValueError:
            pass
        return json.dumps(dic_retorno)   


def GetUserAuthorizations(vLinha):
    vMetodo ='GetUserAuthorizations'
    idtPerson=0
    listServico=[]
    vCount=0
    vMethodos=['']
    email=''
    j=None
#DESCARTAR AS AUTORIZACOES DE ERRO (ex: PI=2)
#    print(vLinha)
    vetLinha = str.split(vLinha,' ')

    if  not vetLinha[8].startswith('PI=2'):
        listServico=[]
        try:
            idtPerson= int(vetLinha[7].replace('I=','').replace('}]',''))
        except:
            try:
                email = vetLinha[7].replace('US=','').replace('}]')
            except:
                email = vetLinha[7].replace('US=','').replace(',','')

           # idtPerson =  re.search(r"(\[RET=).*(\,)",vLinha).group().replace('[RET=','').replace(',')    


            vetPerson = re.search(r"(\[RET=).*",vLinha).group().split(' ')
            idt_person = int(vetPerson[0].replace('[RET=',''))

   
        #print(vetLinha)
        #print(idtPerson)
        vetAuth = re.search(r"(\[RET=).*(\])",vLinha).group().replace('] ]',']').split(':')
        vet=vetAuth[1].split(' ')
        for x in vet:
            p1=x.replace(']','').partition("-")
            p2=p1[2].partition("[")
            
            obj_servico= {"servico": p1[0],"status_servico": p2[0],"produtos":[p2[2]]}
            listServico.append(obj_servico)
        
    
        dataAutorizacao = vetLinha[0] + ' ' + vetLinha[1]
        dataAutorizacao =  dataAutorizacao.replace(' ','T').replace(',','.')+'Z'

        j = json.dumps({'data': dataAutorizacao,'person':idtPerson,'email':email, 'status': 1,'statusDescricao': '', 'servicos':listServico,'metodo':vMetodo})
        #w.write(j+'\n')
        '''
        vCount+=1
        if vCount>20:
            break
        '''
    return j

def AuthenticateAuthorize(vLinha):
    vMetodo ='AuthenticateAuthorize'
    vetLinha = str.split(vLinha,' ')

    m = re.search(r"(\[\{).*(\])",vLinha).group().replace('[{','').replace('}]','').replace(']','').replace('[',', ')
    vet=m.split(', ')
    dicionario={'I':'','US':'','T':'','TP':'','R':'','RET':'', 'DI':'', 'TA':'' }
    for x in vet:
        vet2=x.split('=')
        if len(vet2) > 1:
            dicionario[vet2[0]]=vet2[1]
    
    vetStatus=dicionario['RET'].split('/')

    try:
        dicionario['T'] = int(dicionario['T'])
        dicionario['TP'] = int(dicionario['TP'])
        dicionario['I'] = int(dicionario['I'])
    except:
        pass        
    dataAutorizacao = vetLinha[0] + ' ' + vetLinha[1]
    dataAutorizacao =  dataAutorizacao.replace(' ','T').replace(',','.')+'Z'
    #j = json.dumps({'data': dataAutorizacao,'person':dicionario['I'],'email':dicionario['US'],'ip':dicionario['R'],'device':dicionario['DI'], 'status': int(vetStatus[0]),'statusDescricao': vetStatus[1],'servico': dicionario['T'],'status_servico': '','produto':dicionario['TP'],'metodo':vMetodo })
    #j = 'data':dataAutorizacao + '|' + 'person':dicionario['I'] + '|' + 'email':dicionario['US'] + '|' + 'ip':dicionario['R'] + '|' + 'device':dicionario['DI'] + '|' + 'status': int(vetStatus[0]) + '|' + 'statusDescricao': vetStatus[1] + '|' + 'servico': dicionario['T'] + '|' + 'status_servico': '' + '|' + 'produto':dicionario['TP'] + '|' + 'metodo':vMetodo
    j = str(dataAutorizacao) + '|' + str(dicionario['I']) + '|' + str(dicionario['US']) + '|' + str(dicionario['R']) + '|' + str(dicionario['DI']) + '|' +  str(int(vetStatus[0])) + '|' +  str(vetStatus[1]) + '|' +  str(dicionario['T']) + '|' +  '' + '|' + str(dicionario['TP']) + '|' + str(vMetodo ) 

    return j


def DigestAuthenticate(vLinha):
    vMetodo ='DigestAuthenticate'
    vetLinha = str.split(vLinha,' ')

    m = re.search(r"(\[\{).*(\])",vLinha).group().replace('[{','').replace('}]','').replace(']','').replace('[',', ')
    vet=m.split(', ')
    dicionario={'I':'','US':'','T':'','TP':'','R':'','RET':'', 'DI':'','TA':'' }
    for x in vet:
        vet2=x.split('=')
        if len(vet2) > 1:
            dicionario[vet2[0]]=vet2[1]
    vetStatus =[]        
    vetStatus=dicionario['RET'].split('/')
       
    try:
        
        dicionario['T'] = int(dicionario['T'])
        dicionario['TP'] = int(dicionario['TP']) 
        dicionario['I'] =int(dicionario['I'])


    except:
        pass         
    dataAutorizacao = vetLinha[0] + ' ' + vetLinha[1]
    dataAutorizacao =  dataAutorizacao.replace(' ','T').replace(',','.')+'Z'
    #j = json.dumps({'data':dataAutorizacao ,'person':dicionario['I'],'email':dicionario['US'],'ip':dicionario['R'],'device':dicionario['DI'], 'status': int(vetStatus[0]),'statusDescricao': vetStatus[1], 'servico': dicionario['T'] , 'status_servico': '','produto':dicionario['TP'],'metodo':vMetodo } )
    #j = 'data' + '|' + 'person' + '|' + 'email' + '|' + 'ip' + '|' + 'device' + '|' + 'status' + '|' + 'statusDescricao' + '|' + 'servico' + '|' + 'status_servico' + '|' + 'produto' + '|' + 'metodo'
    j = str(dataAutorizacao) + '|' + str(dicionario['I']) + '|' + str(dicionario['US']) + '|' + str(dicionario['R']) + '|' + str(dicionario['DI']) + '|' +  str(int(vetStatus[0])) + '|' +  str(vetStatus[1]) + '|' +  str(dicionario['T']) + '|' +  '' + '|' + str(dicionario['TP']) + '|' + str(vMetodo )
    return j

def estruturaLogs(vNum):
    
    vetMaquina=[]
    
    if vNum ==1:
        vetMaquina= ['a5-jousty5.host.intranet','a5-jousty6.host.intranet','a5-jousty7.host.intranet','a5-jousty8.host.intranet','a6-jousty5.host.intranet','a6-jousty6.host.intranet','a6-jousty7.host.intranet','tb-b3-jousty1.host.intranet','tb-b3-jousty2.host.intranet','tb-b3-jousty3.host.intranet']
    else:
        vetMaquina= []
    
    dias = ['08']
    horarios = ['00','01','02','03','04','05','06','07','08','09','10','11','12','13','14','15','16','17','18','19','20','21','22','23']
    #dir_output = 'C:\\Users\\bfarias\\Documents\\Demandas\\BI\\Azure\\autenticacao\\gaudi_full\\teste_output\\'
    dir_output = '/data/arquivos_processados/'

    for vmaq in vetMaquina:
            dir_input = '/data/logs/'+  vmaq + '/'
            for dia in dias:
                w = open(dir_output + 'autorizacao_processada_' + dia + '.gz', 'a')    
                #w.write(  str( 'data' + '|' + 'person' + '|' + 'email' + '|' + 'ip' + '|' + 'device' + '|' + 'status' + '|' + 'statusDescricao' + '|' + 'servico' + '|' + 'status_servico' + '|' + 'produto' + '|' + 'metodo'+'\n'))#.encode('cp1252') )
                for horario in horarios:
                        for file in os.listdir(dir_input):
                            if file.endswith(dia + '-' + horario + '.gz'):
                                #with gzip.open(dir_output + dia + '/' + vmaq + '_' + str(file), 'ab') as w:                                    
                                    with gzip.open(dir_input + str(file) , 'rb') as r:
                                        try:
                                            bLinha = r.readline()
                                            vLinha = bLinha.decode('cp1252')
                                            #print(vLinha) 
                                            while vLinha:
                                                try:
                                                    vetLinha = str.split(vLinha,' ')
                                                    if len(vetLinha)>5: 

                                                        dic_retorno=None
                                                        #print(vLinha) 

                                                        if vetLinha[3]=='INFO' and vetLinha[5]=='GetUserInscriptions':
                                                            vdataAutorizacao = vetLinha[0] + " " + vetLinha[1]
                                                            vdataAutorizacao =  vdataAutorizacao.replace(' ','T').replace(',','.')+'Z'
                                                            dic_retorno = GetUserInscriptions(vLinha,vdataAutorizacao)
                                                        elif vetLinha[3]=='INFO' and vetLinha[5]=='GetServiceAuthorization':
                                                            vdataAutorizacao = vetLinha[0] + " " + vetLinha[1] 
                                                            vdataAutorizacao =  vdataAutorizacao.replace(' ','T').replace(',','.')+'Z'
                                                            dic_retorno=GetServiceAuthorization(vLinha,vdataAutorizacao)
                                                        elif vetLinha[3]=='INFO' and vetLinha[5]=='GetUserAuthorizations':
                                                            dic_retorno=GetUserAuthorizations(vLinha)
                                                        '''
                                                        elif vetLinha[3]=='INFO' and vetLinha[5]=='AuthenticateAuthorize':
                                                            dic_retorno= AuthenticateAuthorize(vLinha)
                                                        elif vetLinha[3]=='INFO' and vetLinha[5]=='DigestAuthenticate':
                                                            dic_retorno= DigestAuthenticate(vLinha)    
                                                        '''
                                                        if dic_retorno != None:                                                    
                                                            linha_final = str(dic_retorno) + '\n'
                                                            w.write(  linha_final)#.encode('cp1252'))
                                                            #print(linha_final)    


                                                    bLinha = r.readline()
                                                    vLinha = bLinha.decode('cp1252')
                                                except:
                                                    pass
                                        except:
                                            pass
                                        
                w.close()

if __name__ == '__main__':
    with Pool(11) as p:
            print(p.map(estruturaLogs, [1]))
```
