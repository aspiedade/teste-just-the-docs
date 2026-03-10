
[Documentação](../../documentacao.md) > [Automacao](../automacao.md)

# Ansible

Esta humilde pagina descreve o procedimento utilizado para instalação dos componentes da Confluent via Ansible (BD-371), e deve passar por (muitas) melhorias

Ao tentar utilizar o Ansible como meio de instalação dos componentes da Confluent (Kafka, Zookeeper, KSQL, etc), logo de cara já esbarramos num problema: o Ansible só funciona em Linux, e as nossas workstations são todas Windows, e agora?

**Possíveis caminhos:**

1. Ter uma instancia EC2 na AWS , que seria responsável pelo provisionamento de infra e configuração - problema: custo adicional pra ter uma maquina em QA e outra em PRD, já que as duas redes não se conversam;
2. Rodar o Linux no Windows (WSL  - Windows Subsystem for Linux) - problema: não sei se é problema na minha maquina, mas só de habilitar esse negócio, meu note ficou mais lento, e li alguns comentarios que rodar o Ansible no Windows é um pesadelo;
3. Rodar uma VM - problema: esse troço também é lento e consome recursos que eu não tenho em abundancia no meu note ![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/tongue.svg);
4. Rodar em Docker - eu já tinha o Docker instalado no meu note, todo mundo fala dos benefícios de containers em relação a VM, parece ser a opção com melhor custo beneficio, bora seguir nesse caminho! hehehehe

**Utilizei o PowerShell para rodar estes comandinhos**

```bash
#Primeira coisa, criei um diretório "Docker" pra eu jogar os arquivos deste projeto (C:\Users\cverni\Documents\Docker\)
#lá no diretorio, criar um diretório com o nome qualquer, no meu caso criei como "terransible"
mkdir terransible 
cd terransible
```

**Dentro do diretorio "terransible", criar um arquivo com o nome "Dockerfile"** (só a primeira letra maiusculo, sem extensão)  
*Peguei este script de <https://github.com/kSandr-ki/docker/blob/master/terraform-ansible/Dockerfile>, só mudei a versão do Terraform (para a ultima estável que testamos) e adicionei o vim e awscli na instalação*

**Dockerfile**

```bash
FROM debian:stretch
MAINTAINER kSandr-ki
ENV TERRAFORM_VERSION=0.12.8
WORKDIR /terraform
RUN apt-get update \
    && apt-get install -y wget zip git openssh-client python-cryptography python-openssl procps python-boto sudo make python-pip vim \
    && pip install boto3 botocore git+https://github.com/ansible/ansible.git \
	&& pip install awscli --upgrade \
    && wget -q https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip \
    && unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip -d /bin/ \
    && rm terraform_${TERRAFORM_VERSION}_linux_amd64.zip 


```

**Criando uma imagem**

```bash
# criar uma imagem a partir do Dockerfile, com uma tag que vai servir pra identificar a imagem
# esse "." no final é necessario pq indica que é pra usar o Dockerfile do diretorio atual (espero que vc esteja na pastinha do terransible)
docker build -t cleideverni/terransible .

# o comando -t é de tag, seguido do name:tag, como só passei o parametro name, a coluna TAG ficou com valor latest
# se quisesse passar uma versao (ex: v1.0.0), teria que colocar o comando como :
#docker build -t cleideverni/terransible:v1.0.0 .


#vai demorar um tepo pra baixar e executar tudo, mas depois que terminar, executar o comando para listar as imagens

docker images 

REPOSITORY                                            TAG                 IMAGE ID            CREATED             SIZE
cleideverni/terransible                               latest              1c0d57b72594        4 minutes ago       773MB
```

**Criando um Container a partir da Imagem**

```bash
#Agora que já temos uma imagem com o que a gente precisa, vamos criar um container chamado "terransible" com essa imagem
# o -it é pra abrir o container com um terminal iterativo
docker run --name terransible -it cleideverni/terransible

#se digitar "exit", voce vai sair e parar o container
exit

#para listar todos os container (independente se o status está running ou não) :
docker ps -a 


#se você executou o exit num passo anterior, seu container estará parado:
docker ps -a
CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS                    PORTS               NAMES
69dca8cdd15c        cleideverni/terransible   "bash"              3 weeks ago         Exited (0) 36 hours ago                       terransible


#para iniciá-lo, digite :
#docker start <nome_container>
docker start terransible

#se verificar o status agora, ele estará Up :)
docker ps -a
CONTAINER ID        IMAGE                     COMMAND             CREATED             STATUS              PORTS               NAMES
69dca8cdd15c        cleideverni/terransible   "bash"              3 weeks ago         Up 4 seconds                            terransible

#Beleza, agora vamos entrar de novo no container, para isso, só digitar:
#docker attach <nome_container>
docker attach terransible
```

**Verifique se está tudo configurado conforme esperado**

```bash
root@69dca8cdd15c:/# ansible --version
ansible 2.10.0.dev0
  config file = None
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python2.7/dist-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 2.7.13 (default, Sep 26 2018, 18:42:22) [GCC 6.3.0 20170516]


root@69dca8cdd15c:/# terraform --version
Terraform v0.12.8

```

**Copiando arquivos para dentro do container**

```bash
#docker cp c:\path\to\local\file container_name:/path/to/target/dir/
#ex: quero jogar o arquivo meu-arquivo.txt que está no windows pra dentro do container, num diretório /home/teste/
docker cp C:\Users\cverni\Documents\teste\meu-arquivo.txt  terransible:/cleide/teste/

#agora você já consegue jogar as chaves de acesso pra dentro do seu container, para conectar nas instancias AWS :)
#no meu caso eu joguei os arquivos .pem pra /root/.ssh/ 
#se o Linux reclamar que as permissoes da sua chave estão muito abertas, é só rodar: 
chmod 400 <meu-arquivo>.pem
```

**Copiando o template do confluent ansible**

```bash
#A instalação inicial do Confluent foi baixado por este repositorio, mas voces não precisam deste passo
#git clone https://github.com/confluentinc/cp-ansible.git
```

**Baixando o repositorio do Stash**

```bash
#Muito provavelmente você terá problema de certificado no stash, então rodar este comando antes do git clone
git config --global http.sslverify false
git clone https://stash.uol.intranet/scm/bibd/ansible.git


#Abra a pasta do ansible que acabou de baixar
cd ansible

#testando conexao com os hosts de QA, passando o user ec2-user e o caminho do meu arquivo .pem
ansible all -m ping -u ec2-user --private-key /root/.ssh/<meu-arquivo>.pem -i hosts_qa.yml


#se foi tudo bem, voce vai conseguir rodar o playbook desejado (neste caso o status.yml)
ansible-playbook -i hosts_qa.yml status.yml -u ec2-user --private-key /root/.ssh/<meu-arquivo>.pem


#se você preferir que ao invés de printar a execução na tela, o resultado seja enviado para um arquivo de saida, é só adicionar um "> arquivo.txt" no final:
ansible-playbook -i hosts_qa.yml status.yml -u ec2-user --private-key /root/.ssh/<meu-arquivo>.pem  > saida.txt
```
