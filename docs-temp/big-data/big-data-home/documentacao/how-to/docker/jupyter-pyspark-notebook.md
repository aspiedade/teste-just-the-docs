
[Documentação](../../../documentacao.md) > [How-to](../../how-to.md) > [Docker](../docker.md)

# jupyter-pyspark-notebook

O passo a passo deste documento tem como pre-requisito, o Docker Desktop instalado e rodando.

**Faça o download da imagem mais recente**

```java
docker pull jupyter/pyspark-notebook
```

**Confira se a imagem foi criada como esperado**

```java
docker images

REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
jupyter/pyspark-notebook   latest              e63eb661101e        3 days ago          3.25GB
```

**Comando basico para iniciar o notebook**

```java
docker run -it -p 8888:8888 jupyter/pyspark-notebook
```

Se quiser evitar o risco de esquecer de baixar o seu notebook para salver tudo que foi feito, é interessante que crie um volume para salvar seus trabalhos em um diretório do seu computador windows.

Para isso, crie um diretorio para seu Pyspark no seu computador (no meu caso eu criei em C:\Users\cverni\Documents\Docker\pyspark), e salve o arquivo abaixo com o nome docker-compose.yaml, que vai criar um sub diretorio work, para salvar os seus notebooks.

**docker-compose.yaml**

```java
version: '3'
services:
  spark:
    image: jupyter/pyspark-notebook
    ports:
      - "8888:8888"
      - "4040-4080:4040-4080"
    volumes:
      - ./work:/home/jovyan/work/
```

> [!TIP]
> Ah, mas pra que criar essa pastinha "work"? Não posso colocar direto como "./[:/home/jovyan/](http://work/home/jovyan/work/)"?  
> Pode, mas o seu arquivo docker-compose.yaml vai aparecer na lista de arquivos no Jupyter, e isso não é legal. Por isso criamos esse subdiretorio isolar o arquivo de config e evitar alguma manipulação acidental ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/wink.svg)

No power-shell, vá ate o diretorio do Pyspark, e execute o comando abaixo

**Comando para executar o docker-compose.yaml**

```java
docker-compose up
```

Depois de executar o "docker run" ou "docker-compose" vai iniciar o jupyter, você só precisa copiar a URL e abrir no seu navegador de preferência.

Documentação: <https://hub.docker.com/r/jupyter/pyspark-notebook>
