# References
https://opencredo.com/blogs/kafka-vs-rabbitmq-the-consumer-driven-choice/

# Summary
For distributed systems processing with massive amounts of data at scale with goals of throughput, ordering, high availability - Kafka is recommended.

## Throughput: how fast messages are transferred from producer to consumer.
Both can achieve throughput of millions of msg's consumed per sec using competiting consumers pattern but kafka has out of the box support of partitioning and consumer groups. For rabbitmq, have to leverage
consistent-hash exchange and/or sharded plugin

## Latency
Rabbitmq scales vertically(CPU and memory), better latency for smaller throughput scenarios. However, Queue baclog should be kept small always to avoid memory contentions. 
Kafka scale out horizontally and trades lower latency for better durability and availability.

## Ordering of messages: 
Kafka provides ordering of messages at a topic-partition level. Rabbitmq introducted "Single Active Consumer" for the same.

## Routing: 
Rabbitmq has exchange driven multiple routing topologies support. Kafka does not implement routing out of the box, consumer has to handle it. Intermediate processes often need to filter and then 
forward/duplicate a subset of messages to a new topic.

## HA of consumers
Failover to a standby consumer is supported in both, in rabbitmq via "Single Active Consumer" and in kafka via "Consumer group".

## Observability - Queue depth and throughput
Both expose the same via rabbitmq dashboard and via JMX in kafka.

## Retention and storage
Rabbitmq, as soon as msg. acked by consumer it is removed from queue.
Kafka has Policy-based message retention. It can be configured to keep several days’ worth of message history, allowing messages to be processed at a later date – 
perhaps some consumers were offline, or some reprocessing is needed.  However, it is vitally important to watch consumer group lag in this case.
