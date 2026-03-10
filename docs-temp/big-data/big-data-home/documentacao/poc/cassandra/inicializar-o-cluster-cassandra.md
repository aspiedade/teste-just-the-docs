
[Documentação](../../../documentacao.md) > [POC](../../poc.md) > [Cassandra](../cassandra.md)

# Inicializar o cluster Cassandra

Na conta de experimentação, acessar via ssh as seguintes máquinas:

10.80.0.18 (cassandra-node1-dev), 10.80.0.14 (cassandra-node2-dev) e 10.80.0.24 (cassandra-node3-dev) e executar os seguintes comandos em cada máquina (começando pela cassandra-node1-dev):

```bash
#inicializar o cassandra
 sudo su -
 su cassandra_admin
 /home/cassandra_admin/cassandra-datastax-5.1.13/./bin/cassandra &



```

Após alguns segundos, se as três máquinas apresentarem uma mensagem semelhante a que está no bloco abaixo, significa que o cluster está no ar:

```bash
INFO  [main] 2019-06-28 15:18:32,229 StorageService.java:718 - Snitch informatio                                                                                        n: DynamicEndpointSnitch{registered=true, subsnitch=SimpleSnitch{, DC='datacente                                                                                        r1', rack='rack1'}}, local DC:datacenter1 / rack:rack1



```
