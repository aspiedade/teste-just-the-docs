
[Documentação](../../../../documentacao.md) > [Azure](../../../azure.md) > [POC Cloudera](../../poc-cloudera.md) > [Preparacao das maquinas](../preparacao-das-maquinas.md)

# Instalacao do python 3.7

**Instalação**

```bash
#Instalando dependências + Python3.7.1
yum install -y gcc openssl-devel bzip2-devel libffi libffi-devel

#ir pro diretorio de binarios
cd /usr/bin/
wget https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tgz
tar xzf Python-3.7.1.tgz
cd Python-3.7.1
./configure --enable-optimizations
make altinstall

#removendo o arquivo de instalacao
rm /usr/bin/Python-3.7.1.tgz
```

## Precisa remover o Python ? (tipo, errou a versão ou instalou no diretorio errado)

Não se desespere, é só remover os arquivos  ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)

**Removendo o Python**

```bash
#primeiro ache onde estão os arquivos do Python (neste caso, quero remover tudo que é do Python-3.7
whereis python
#RESULTADO: 
python: /usr/bin/python /usr/bin/python2.7 /usr/bin/python2.7-config /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/local/bin/python3.7m /usr/local/bin/python3.7 /usr/local/bin/python3.7m-config /usr/local/lib/python3.7 /usr/include/python2.7 /usr/share/man/man1/python.1.gz


#pra não ter erro e remover o que não deve, pode especificar a versão, que no caso é o python3
whereis python3
#RESULTADO: 
python3: /usr/local/bin/python3.7m /usr/local/bin/python3.7 /usr/local/bin/python3.7m-config /usr/local/lib/python3.7

#agora é só remover os diretorios (cuidado com esse comando rm, PELOAMORDEDEUS!!!)

rm -rf /usr/local/bin/python3.7m
rm -rf /usr/local/bin/python3.7
rm -rf /usr/local/bin/python3.7m-config
rm -rf /usr/local/lib/python3.7


```
