
[Documentação](../../../../documentacao.md) > [Azure](../../../azure.md) > [POC Cloudera](../../poc-cloudera.md) > [Preparacao das maquinas](../preparacao-das-maquinas.md)

# Alterando o timezone da VM

Quando criamos uma VM, o timezone padrão é  "Etc/UTC" , o que tem uma diferença de +3 horas em relação ao horário de Brasilia (e que cá entre nos, é um saco qdo precisamos analisar horarios em logs)

O menino do suporte recomendou-me a baixar todos os serviços antes de fazer essa alteração, e preferencialmente executar como root para evitar o risco da alteração rolar apenas pro dominio do usuario.  (e como sou obediente, foi o que fiz kkk)

**Depois de logar na maquina como root, digite "date" para conferir qual o horario atual da maquina:**

```bash
[root@bi-logstash2-poc ~]# date
Tue May 15 17:06:09 UTC 2018
```

**Exibir qual o timezone atual**

```bash
[root@bi-logstash2-poc ~]# ls -l /etc/localtime
lrwxrwxrwx. 1 root root 29 Jan 12 14:42 /etc/localtime -> ../usr/share/zoneinfo/Etc/UTC

```

**Listar quais a opções de timezone que disponíveis na maquina:**

```bash
[root@bi-logstash2-poc ~]# timedatectl list-timezones
Africa/Abidjan
Africa/Accra
Africa/Addis_Ababa
Africa/Algiers
(...)
America/Santo_Domingo
America/Sao_Paulo
America/Scoresbysund
(...)
```

**Alterando o timezone para America/Sao\_Paulo**

```bash
[root@bi-logstash2-poc ~]# timedatectl set-timezone America/Sao_Paulo

#conferindo se foi alterado certinho:
[root@bi-logstash2-poc ~]# date
Tue May 15 14:09:14 -03 2018
[root@bi-logstash2-poc ~]# ls -l /etc/localtime
lrwxrwxrwx 1 root root 39 May 15 14:09 /etc/localtime -> ../usr/share/zoneinfo/America/Sao_Paulo

```

Depois é só repetir isso em todas as maquinas do cluster  [^![](/confluence/s/-n5rgog/8804/vbak45/_/images/icons/emoticons/smile.svg)]

Peguei essas informações deste link: <https://www.cyberciti.biz/faq/centos-linux-6-7-changing-timezone-command-line/>
