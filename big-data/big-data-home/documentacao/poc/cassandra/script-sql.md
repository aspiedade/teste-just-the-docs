
[Documentação](../../../documentacao.md) > [POC](../../poc.md) > [Cassandra](../cassandra.md)

# Script SQL

```sql
CREATE KEYSPACE auth_adm WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '2'}  AND durable_writes = true;

CREATE FUNCTION auth_adm.udf_count_distinct_state(state set<text>, val set<text>)
    CALLED ON NULL INPUT
    RETURNS set<text>
    LANGUAGE java
    AS $$
  if (val !=null) {
  for (String value : val)
        state.add(value);
   }
  return state; $$;

CREATE FUNCTION auth_adm.udf_list_size(input list<int>)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$
   return input.size();$$;

CREATE FUNCTION auth_adm.udf_count_distinct_final(state set<bigint>)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$ return state.size();$$;

CREATE FUNCTION auth_adm.udf_count_distinct_state(state set<bigint>, val set<bigint>)
    CALLED ON NULL INPUT
    RETURNS set<bigint>
    LANGUAGE java
    AS $$
  if (val !=null) {
  for (Long  value : val)
        state.add(value);
   }
  return state; $$;

CREATE FUNCTION auth_adm.udf_count_by_status(input list<int>, status int)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$
   return Collections.frequency(input, status);$$;

CREATE FUNCTION auth_adm.udf_list_size(input list<text>)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$
   return input.size();$$;

CREATE FUNCTION auth_adm.udf_count_distinct_final(state set<text>)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$ return state.size();$$;

CREATE FUNCTION auth_adm.udf_list_size(input list<timestamp>)
    CALLED ON NULL INPUT
    RETURNS int
    LANGUAGE java
    AS $$
   return input.size();$$;

CREATE AGGREGATE auth_adm.uda_count_distinct(set<bigint>)
    SFUNC udf_count_distinct_state
    STYPE set<bigint>
    FINALFUNC udf_count_distinct_final
    INITCOND {};

CREATE AGGREGATE auth_adm.uda_count_distinct(set<text>)
    SFUNC udf_count_distinct_state
    STYPE set<text>
    FINALFUNC udf_count_distinct_final
    INITCOND {};

CREATE TABLE auth_adm.authentication_by_person (
    idt_person bigint,
    dat_partition timestamp,
    countries set<text>,
    dat_authentication list<timestamp>,
    devices set<text>,
    ips set<text>,
    status_authentication list<int>,
    PRIMARY KEY (idt_person, dat_partition)
) WITH CLUSTERING ORDER BY (dat_partition DESC)
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND dclocal_read_repair_chance = 0.1
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99PERCENTILE';

CREATE TABLE auth_adm.aggregation_by_device (
    device_id text,
    dat_partition timestamp,
    countries set<text>,
    dat_authentication list<timestamp>,
    ips set<text>,
    persons set<bigint>,
    status_authentication list<int>,
    PRIMARY KEY (device_id, dat_partition)
) WITH CLUSTERING ORDER BY (dat_partition DESC)
    AND bloom_filter_fp_chance = 0.01
    AND caching = {'keys': 'ALL', 'rows_per_partition': 'NONE'}
    AND comment = ''
    AND compaction = {'class': 'org.apache.cassandra.db.compaction.SizeTieredCompactionStrategy', 'max_threshold': '32', 'min_threshold': '4'}
    AND compression = {'chunk_length_in_kb': '64', 'class': 'org.apache.cassandra.io.compress.LZ4Compressor'}
    AND crc_check_chance = 1.0
    AND dclocal_read_repair_chance = 0.1
    AND default_time_to_live = 0
    AND gc_grace_seconds = 864000
    AND max_index_interval = 2048
    AND memtable_flush_period_in_ms = 0
    AND min_index_interval = 128
    AND read_repair_chance = 0.0
    AND speculative_retry = '99PERCENTILE';



```
