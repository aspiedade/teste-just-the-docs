
[Documentação](../../documentacao.md) > [How-to](../how-to.md)

# Como estender um sistema de arquivos Linux apos um redimensionamento de volume

PROCEDIMENTO EM REVISÂO, NÂO EXECUTAR O MESMO EM DISCOS QUE NÂO FOREM DE SISTEMA, ISSO PODERÁ INATIVAR O AMBIENTE.

ATT,

Evandro.

Primeiro acesse a instancia via SSH, e execute o comado "**df -h**" (atenção que -h e -H são coisas distintas), para conferir se o volume que você quer manipular está no tamanho esperado (vai que...)

```
Neste exemplo, o volume "/dev/nvme2n1p1" ainda está indicando o tamanho de 20G
```

**listando tamanho do sistema de arquivos**

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  452K  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/nvme0n1p1   10G  3.5G  6.6G  35% /
/dev/nvme2n1p1   20G  393M   19G   3% /data
/dev/nvme1n1p1  9.8G   37M  9.2G   1% /logs
tmpfs           389M     0  389M   0% /run/user/1000

```

Se o seu volume tiver uma partição, precisamos verificar seu tamanho listando os blocos anexados a ele, atraves do comando "**lsblk**". Veja que a partição "nvme2n1p1" é menor que o tamanho do disco, indicando que há a necessidade de espanção:

**listando os blocos (tamanho da partição)**

```bash
$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme2n1       259:0    0  21G  0 disk
└─nvme2n1p1   259:2    0  20G  0 part /data
nvme1n1       259:1    0  10G  0 disk
└─nvme1n1p1   259:3    0  10G  0 part /logs
nvme0n1       259:4    0  10G  0 disk
├─nvme0n1p1   259:5    0  10G  0 part /
└─nvme0n1p128 259:6    0   1M  0 part

```

Para isso, executamos o comando "growpart", passando como parametros o disco/partição e o numero da partição:

**estendendo a partição**

```bash
$ sudo growpart /dev/nvme2n1 1
CHANGED: partition=1 start=2048 old: size=41940992 end=41943040 new: size=44038111 end=44040159

```

Se executarmos novamente os comandos de "lsbk", vemos que a partição foi estendida, mas ao rodarmos o "df -h" o sistemas de arquivos ainda mostra o tamanho antigo de 20GB:

**conferindo a alteração**

```bash
$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme2n1       259:0    0  21G  0 disk
└─nvme2n1p1   259:2    0  21G  0 part /data
nvme1n1       259:1    0  10G  0 disk
└─nvme1n1p1   259:3    0  10G  0 part /logs
nvme0n1       259:4    0  10G  0 disk
├─nvme0n1p1   259:5    0  10G  0 part /
└─nvme0n1p128 259:6    0   1M  0 part

$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  452K  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/nvme0n1p1   10G  3.5G  6.6G  35% /
/dev/nvme2n1p1   20G  388M   19G   3% /data
/dev/nvme1n1p1  9.8G   37M  9.2G   1% /logs
tmpfs           389M     0  389M   0% /run/user/1000


```

Para aumentar o sistema de arquivos, utilizamos o comado "resize2fs" passando como parametro o nome do device:

**estendendo o sistema de arquivos**

```bash
sudo resize2fs /dev/nvme2n1p1


```

Agora é só confirir a alteração com "df -h":

**conferindo a alteração**

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        1.9G     0  1.9G   0% /dev
tmpfs           1.9G     0  1.9G   0% /dev/shm
tmpfs           1.9G  452K  1.9G   1% /run
tmpfs           1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/nvme0n1p1   10G  3.5G  6.6G  35% /
/dev/nvme2n1p1   21G  459M   20G   3% /data
/dev/nvme1n1p1  9.8G   37M  9.2G   1% /logs
tmpfs           389M     0  389M   0% /run/user/1000

```

Fonte: <https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html>
