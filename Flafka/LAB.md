## Lab
---
1. Sending Messages from Flume to Kafka
___

### 1.1. Run the Flume Agent

```console

$ flume-ng agent --conf /etc/flume-ng/conf \
> --conf-file $DEVSH/exercises/flafka/spooldir_kafka.conf \
> --name agent1 -Dflume.root.logger=INFO,console

```
#### spooldir_kafka.conf

```vi
# spooldir_kafka.conf: A Spooling Directory Source with a Kafka Sink

# Name the components on this agent
agent1.sources = webserver-log-source
agent1.sinks = kafka-sink
agent1.channels = memory-channel

# Configure the source
agent1.sources.webserver-log-source.type = spooldir
agent1.sources.webserver-log-source.spoolDir = /flume/weblogs_spooldir
agent1.sources.webserver-log-source.channels = memory-channel

# Configure the sink
agent1.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink
agent1.sinks.kafka-sink.topic = weblogs
agent1.sinks.kafka-sink.brokerList = localhost:9092
agent1.sinks.kafka-sink.batchSize = 20
agent1.sinks.kafka-sink.channel = memory-channel


# Use a channel which buffers events in memory
agent1.channels.memory-channel.type = memory
agent1.channels.memory-channel.capacity = 100000
agent1.channels.memory-channel.transactionCapacity = 1000
```


### 1.2. Test the Flume Agent Kafka Sink

```console
$ $DEVSH/scripts/copy-move-weblogs.sh \
> /flume/weblogs_spooldir
```

![image](https://user-images.githubusercontent.com/28293389/55779197-c755b300-5adf-11e9-91a1-13550507690f.png)