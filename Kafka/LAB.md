## Lab
___
1. Kafka Producer & Consumer
---

### 1.1.  Creating a Kafka Topic

```console
$ kafka-topics --create \
> --zookeeper localhost:2181 \
> --replication-factor 1 \
> --partitions 1 \
> --topic weblogs

```


### 1.2. Producing and Consuming Messages

#### Producer
```console
$ kafka-console-producer \
> --broker-list localhost:9092 \
> --topic weblogs
```

#### Consumer
```console
$ kafka-console-consumer \
> --zookeeper localhost:2181 \
> --topic weblogs \
> --from-beginning
```

