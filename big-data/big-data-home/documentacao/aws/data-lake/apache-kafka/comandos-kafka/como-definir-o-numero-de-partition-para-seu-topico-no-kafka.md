
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Kafka](../../apache-kafka.md) > [Comandos Kafka](../comandos-kafka.md)

# Como definir o numero de partition para seu topico no Kafka

> [!TIP]
> Here is the calculation we use to optimize the number of partitions for a Kafka implementation.
>
> ### # Partitions = Desired Throughput / Partition Speed
>
> Conservatively, you can estimate that a single partition for a single [Kafka topic](https://dattell.com/data-architecture-blog/creating-a-kafka-topic-what-are-kafka-topics-how-are-they-created/) runs at 10 MB/s.
>
> As an example, if your desired throughput is 5 TB per day. That figure comes out to about 58 MB/s. Using the estimate of 10 MB/s per partition, this example implementation would require 6 partitions.

Fonte: <https://www.linkedin.com/pulse/kafka-optimization-how-many-partitions-needed-maria-hatfield-phd/>

Fonte 2: <https://dev.to/kafkabr/kafka-quantas-particoes-por-topico-537c#:~:text=Portanto%2C%20o%20n%C3%BAmero%20de%20parti%C3%A7%C3%B5es%20%C3%A9%206%20>.
