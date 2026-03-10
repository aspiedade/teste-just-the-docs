
[Documentação](../../../documentacao.md) > [AWS](../../aws.md) > [Data Lake](../data-lake.md)

# Apache Kafka

> [!TIP]
> Topic Name
>
>  
>
> Padrão sugerido para nome de topicos: <Aplicação ou schema do banco de dados>.<data name>.<message type>, sendo:
>
> <owner> = owner do dado, que pode ser uma aplicação ou um schema do banco de dados (ex: gaudi ou uolbr)  
> <data name> = identificador do dado.   
> <message type> = tipo da mensagem, que pode ser dividido em:
>
> - **log -** For logging data (slf4j, syslog, etc)
> - **queuing -** For classical queuing use cases.
> - **tracking -** For tracking events such as user clicks, page views, ad views, etc.
> - **etl/db -** For ETL and [CDC](https://en.wikipedia.org/wiki/Change_data_capture) use cases such as database feeds.
> - **streaming -** For intermediate topics created by stream processing pipelines.
> - **push -** For data that’s being pushed from offline (batch computation) environments into online environments.
> - **user -** For user-specific data such as scratch and test topics.
>
> No caso do log de autenticação do Gaudi, vai ser "gaudi.authentication.log"

### Veja Também

- [Comandos Kafka](apache-kafka/comandos-kafka.md)
  - [Como definir o numero de partition para seu topico no Kafka](apache-kafka/comandos-kafka/como-definir-o-numero-de-partition-para-seu-topico-no-kafka.md)
  - [Adicionar/remover brokers do cluster](/confluence/pages/viewpage.action?pageId=231164888)
  - [Remover um topic na marra depois dele ter sido marcado para deletion](apache-kafka/comandos-kafka/remover-um-topic-na-marra-depois-dele-ter-sido-marcado-para-deletion.md)
- [Kafka-Connect](apache-kafka/kafka-connect.md)
  - [Tratando erros do Kafka Connect com Kafkacat](apache-kafka/kafka-connect/tratando-erros-do-kafka-connect-com-kafkacat.md)
  - [Conectores](apache-kafka/kafka-connect/conectores.md)
    - [Consultar o status de um conector](apache-kafka/kafka-connect/conectores/consultar-o-status-de-um-conector.md)
    - [Sink-S3](apache-kafka/kafka-connect/conectores/sink-s3.md)
  - [Iniciar ou Parar o kafka connect](apache-kafka/kafka-connect/iniciar-ou-parar-o-kafka-connect.md)
  - [Instalacao - pacote](apache-kafka/kafka-connect/instalacao-pacote.md)
  - [Instalacao via yum](apache-kafka/kafka-connect/instalacao-via-yum.md)
  - [Glue Schema Registry](apache-kafka/kafka-connect/glue-schema-registry.md)
