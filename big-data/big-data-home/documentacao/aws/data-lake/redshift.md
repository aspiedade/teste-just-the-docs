
[Documentação](../../../documentacao.md) > [AWS](../../aws.md) > [Data Lake](../data-lake.md)

# Redshift

##### **Compactação de colunas**

Seguiremos o recomendado pela AWS, utilizaremos o comando COPY para aplicar a compactação automática.

Sendo assim, sempre utilizaremos o parâmetro COMPUPDATE como ON.

Maiores detalhes: <https://docs.aws.amazon.com/pt_br/redshift/latest/dg/t_Compressing_data_on_disk.html>

##### **Colunas CHAR, VARCHAR e NVARCHAR**

Esse é um ponto bem diferente que muda de outros bancos, o tamanho que declaramos do campo não implica na contagem máxima de caracteres permitidos. O tamanho decladro é em bytes, e para armazenamento é contado + 4 bytes.

No exemplo "colunaString NVARCHAR(50)" temos a "colunaString" que cabe até 50 bytes de caracteres - lembrando que cada caractere pode ocupar de 1 a 4 bytes - e, em seu tamanho máximo, ocupará 50bytes + 4 bytes em disco. Vale ressaltar que para armazenamento o Redshift ocupa somente a quantidade de bytes armazenados na coluna, porém em tempo de consulta ele aloca memória para a quantidade declarada no campo.

Por esse motivo adotamos a notação de +0,5 para colunas onde sabemos o número total de caracteres. Ou seja, se na "colunaString" eu quero armazenar no máximo 100 caracteres, a declarão fica "colunaString NVARCHAR(150)". Vale verificar se essa notação é aderente à tabela que for criar.

Maiores detalhes:

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_Supported_data_types.html>

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/r_Character_types.html>

##### **Chave Primária**

É unica e não funciona igual aos bancos trandicionais, ou seja, não garante unicidade do registro e nem cria índice.

Sua utilização é exclusivamente semântica para uma melhor compreensão dos dados armazenados.

##### **Chave Estrangeira**

Pode ser composta e sua utilização é exclusivamente semântica para uma melhor compreensão dos dados armazenados.

**OBS**.: Defina as limitações da chave primária e estrangeira entre as tabelas sempre que apropriado. Embora elas sejam apenas informativas, o otimizador de consulta utiliza estas limitações para gerar planos de consulta mais eficientes. O padrão de nomenclatura que adotamos para constrainsts sera: <nome da tabela>\_<PK/FK>\_<001> onde 001 depende das N repetições que tivermos nessa mesma tabela.

Maiores detalhes:

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/t_Defining_constraints.html>

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_best-practices-defining-constraints.html>

#### **Chaves de distribuição (DISTKEY)**

A ideia da chave de distribuição é diminuir a fase de redistribuição que temos no plano de execução da query.

Essa é a fase que é executada após a seleção dos dados brutos entre as fatias de cada nó e é responsável em redistribuir esses dados para que o processamento seja o mais igual possível em cada nó de computação.

Existem os estilos abaixo de distribuição:

- ##### **KEY**

  As linhas são distribuídas de acordo com os valores em uma coluna.
- ##### **ALL**

  Uma cópia de toda a tabela é distribuída para cada nó.
- ##### **EVEN**

  As linhas são distribuídas entre todas as fatias em ida e volta, independentemente dos valores de qualquer coluna em especial. A distribuição EVEN é apropriada quando uma tabela não participa em junções ou quando não há uma opção clara entre a distribuição KEY e ALL. A distribuição EVEN é o estilo de distribuição padrão.

  #####
- ##### **AUTO**

  O Amazon Redshift atribui um estilo de distribuição ideal com base no tamanho dos dados da tabela. Por exemplo, o Amazon Redshift inicialmente atribui a distribuição ALL a uma pequena tabela e, em seguida, altera para a distribuição EVEN quando a tabela aumenta de tamanho.

  Maiores detalhes: <https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_choosing_dist_sort.html>

  **OBS.:** Uma forma de avaliar qual estilo de distribuição escolher, é executar o plano de execução (adicionar a palavra explain antes do statement) e avaliar se existem muitas chamadas DS\_BCAST\* (transmissão) e DS\_DIST\* (redistribuição). A ideia é diminuir esses números de chamadas que, consequentemente, acarretará em uma melhor performance da query/tabela.  
  Maiores detalhes: <https://docs.aws.amazon.com/pt_br/redshift/latest/dg/tutorial-tuning-tables-distribution.html>

#### **Chaves de Classificação (SORTKEY)**

O Redshift armazena seus dados em disco na ordem classificada de acordo com a chave de classificação - usa os primeiros oito bytes da chave de classificação. O otimizador de consultas do Amazon Redshift usa a ordem de classificação ao determinar planos de consulta ideais.

- Se dados recentes forem mais consultados, especifique a coluna de time stamp como a coluna principal da chave de classificação.
- Se você fizer filtragem de intervalos frequentes ou filtragem de igualdade em uma coluna, especifique esta coluna como a chave de classificação.
- Se você se conecta frequentemente a uma tabela (join), especifique a coluna de junção como a chave de classificação e a chave de distribuição.

##### 

##### **Composta**

Uma chave composta contém todas as colunas listadas na definição de chave de classificação, na ordem em que estão listadas. Uma chave de classificação composta é mais útil quando o filtro de uma consulta aplica condições, tais como filtros e junções, que usam um prefixo das chaves de classificação na ordem em que foram declaradas. Os benefícios de desempenho da classificação compostos diminuem quando as consultas dependerem somente de colunas de classificação secundárias (fora de ordem ou que pulam as primeiras colunas que foram declaradas) , sem consultar as colunas principais (em ordem de declaração). COMPOUND é o tipo de classificação padrão.

##### **Intercalada**

Uma classificação intercalada concede igual peso a cada coluna ou subconjunto de colunas na chave de classificação. Se várias consultas usam colunas diferentes como filtros, é frequentemente possível melhorar o desempenho dessas consultas usando um estilo intercalado de classificação. Quando uma consulta usa predicados restritivos em colunas de classificação secundárias, a classificação intercalada melhora significativamente o desempenho da consulta em relação à classificação composta.

Importante: As melhorias de desempenho que você ganha ao executar uma chave de classificação intercalada devem ser pesadas contra o aumento nos tempos de carregamento e limpeza. Devemos executar operações de VACUUM e ANALYZE regularmente, especialmente após grandes carregamentos de dados, para reclassificar e reanalisar os dados.

Maiores detalhes:

<https://docs.aws.amazon.com/pt_br/redshift/latest/dg/t_Sorting_data.html><https://docs.aws.amazon.com/pt_br/redshift/latest/dg/c_best-practices-sort-key.html><https://docs.aws.amazon.com/pt_br/redshift/latest/dg/r_vacuum_diskspacereqs.html><https://docs.aws.amazon.com/pt_br/redshift/latest/dg/t_Analyzing_tables.html>

**Exemplo**

```sql
CREATE TABLE uolcorp.gaudi_authentication(
idt_person BIGINT
,dat_partition NVARCHAR(10) distkey
,idt_service INT
,idt_product INT
,username NVARCHAR(225)
,username_length INT
,nam_domain NVARCHAR(375)
,nam_domain_length INT
,des_type_user NVARCHAR(23)
,idt_application INT
,ip_username NVARCHAR(45)
,idt_status_authentication INT
,ip_client NVARCHAR(45)
,device_id NVARCHAR(50)
,nam_gaudi_host NVARCHAR(53)
,nam_gaudi_service NVARCHAR(45)
,des_reputation_ip NVARCHAR(75)
,port_ip_username INT
,num_elapsedtime BIGINT
,geoip_country_name NVARCHAR(150)
,geoip_city_name NVARCHAR(150)
,geoip_continent_code NVARCHAR(2)
,geoip_latitude DOUBLE PRECISION
,geoip_longitude DOUBLE PRECISION
,geoip_region_code NVARCHAR(3)
,geoip_region_name NVARCHAR(150)
,dat_authentication TIMESTAMP
,dat_created TIMESTAMP

,CONSTRAINT gaudi_authentication_PK PRIMARY KEY(idt_person,dat_partition,idt_service,dat_authentication)
)

interleaved sortkey(dat_partition, idt_service, idt_person, dat_authentication);
```
