
[Documentação](../../documentacao.md) > [Projetos](../projetos.md)

# Problemas conhecidos

1. **Erro no Kudu após desligar as maquinas do cluster**

   Após iniciar/reiniciar as instancias EC2 e iniciar os serviços no Cloudera Manager, você poderá receber uma bela mensagem de erro no cluster, pois não foi possível subir os serviços do Kudu e Zookeeper.

   Esse erro ocorre pois o NTP (Network Time Protocol) ainda não estará sincronizado... Mas calma! Basta aguardar alguns minutos que a sincronização será feita de forma automática, e você poderá iniciar os serviços do Cloudera com sucesso ![](https://confluence.intranet.uol.com.br/confluence/s/en_US/7801/0ac8ad8167063a2d16fb426a10b9044cf4bf1b17/_/images/icons/emoticons/smile.svg)

   Para saber o status do NTP, basta conectar numa instancia do cluster via ssh, e digitar o comando "ntpstat"

   **Comando para saber o status do NTP**

   ```bash
   [centos@worker-node1-dev ~]$ ntpstat
   unsynchronised
     polling server every 64 s
   [centos@worker-node1-dev ~]$ ntpstat
   synchronised to NTP server (69.36.182.57) at stratum 3
     time correct to within 50 ms
     polling server every 64 s
   [centos@worker-node1-dev ~]$
   ```

   **Mas se não tiver tempo de esperar...**

   ```bash
   #este comando irá restartar o NTP imediatamente
   sudo systemctl restart ntpd


   #exemplo:
   [centos@worker-node2-dev ~]$ ntpstat
   unsynchronised
      polling server every 64 s
   [centos@worker-node2-dev ~]$ sudo systemctl restart ntpd
   [centos@worker-node2-dev ~]$ ntpstat
   synchronised to NTP server (195.21.137.209) at stratum 3
      time correct to within 1025 ms
      polling server every 64 s
   ```
