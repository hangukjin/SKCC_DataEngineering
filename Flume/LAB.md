## Lab
___
1. Create a new flume configuration file with the following:


- Source  
    - Type : Netcat  
    - Bind : localhost  
    - Port : 44444  

- Channel  
    - Type : Memory  
    - Capacity : 1000  
    - transactionCapacity : 100  
    
- Sink  
    - Type : logger  


---

### 1.1.  Make Configuration file

```vi

# Name the components on this agent
agent1.sources = netcat-source
agent1.sinks = logger-sink
agent1.channels = memory-channel

# Describe/configure the source
agent1.sources.netcat-source.type = netcat
agent1.sources.netcat-source.bind = localhost
agent1.sources.port = 44444
agent1.sources.netcat-source.channels = memory-channel

# Describe the sink
agent1.sinks.logger-sink.type = logger
agent1.sinks.hdfs-sink.channel = memory-channel

# Use a channel which buffers events in memory
agent1.channels.memory-channel.type = memory
agent1.channels.memory-channel.capacity = 1000
agent1.channels.memory-channel.transactionCapacity = 100

```

![image](https://user-images.githubusercontent.com/28293389/55768699-dc6b1b80-5ab8-11e9-87ae-dba76e284a37.png)


### 1.2. Agent 실행

```console
$ flume-ng agent \
> --conf /etc/flume-ng/conf \
> --conf-file $DEVSH/exercises/flume/netcat.conf \
> --name agent1 -Dflume.root.logger=INFO,console
```

![image](https://user-images.githubusercontent.com/28293389/55768791-2f44d300-5ab9-11e9-94a6-29a00edae301.png)

### 1.3. Send Message

![image](https://user-images.githubusercontent.com/28293389/55768974-fa854b80-5ab9-11e9-9451-988f11276283.png)

