
[Documentação](../../../documentacao.md) > [POC](../../poc.md) > [Cassandra](../cassandra.md)

# Acesso ao Cassandra via cql

Em qualquer máquina do cluster, digite o seguintes comandos:

```bash
sudo su -
su cassandra_admin


/home/cassandra_admin/cassandra-datastax-5.1.13/./bin/cqlsh 10.80.0.18
```

Se não houver problema, uma mensagem semelhante a do bloco seguinte será apresentada:

```bash
Connected to CLUSTER-AUTHENTICATION at 10.80.0.18:9042.
[cqlsh 5.0.1 | Cassandra 3.11.3.5113 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
cqlsh>



```

Hosts: 10.80.0.18 (cassandra-node1-dev), 10.80.0.14 (cassandra-node2-dev) e 10.80.0.24 (cassandra-node3-dev) 
