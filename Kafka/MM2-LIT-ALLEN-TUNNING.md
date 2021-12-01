


## LIT Observations
3 topic with lag
- visibility.ownership.document
- visibility.ownership.envelope
- LENS.Transactions.PartialUpdates.Partitioned PartitionCount


## MM 

- Properties hardcode
```
  LIT.request.timeout.ms: "60000"
  ALL.max.block.ms: "120000"
  LIT.offset.flush.timeout.ms: "50000"
  ALL.offset.flush.timeout.ms: "50000"
```

## Mirror Maker default configuration.

Have a look at MM container definition.

We should need to know if producer default values has been modify

    acks = all
      -> Requests will need to wait until the record has been replicated before being acknowledged, which would increase the overall request latency

    batch.size = 16384
      -> 4k size means only 4 messages will send.
    
    buffer.memory = 33554432
      -> 8 messages at most
    
    client.id = my-connector-producer
    
    linger.ms = 0
    
    max.block.ms = 9223372036854775807
    
    max.in.flight.requests.per.connection = 1
       -> ensure order, decrease troughput.
       -> we could increase and enable to ensure the order, and increase throughput.
          enable.idempotent=true

    max.request.size = 1048576



  



## Action to be taken in the source





## Action to be taken in MirrorMaker

- Adjusts the default configuration
- Increase number of MM instances to produce more messages.
- Enable read only commit messages
  - lit.consumer.isolation.level: read_committed
- Enable EoS (delivery once) transaction model, no duplicates/idempotence 
  - lit->all.transaction.producer.enabled: true




## Actions to be taken in both clusters.

clusters=lit,all

lit->all.consumer.overrides.auto.offset.reset = latest
lit->all.consumer.auto.offset.reset = latest
lit->all.source.consumer.auto.offset.reset = latest


lit->all.target.consumer.auto.offset.reset = latest
lit->all.source.cluster.consumer.overrides.auto.offset.reset = latest
lit->all.target.cluster.consumer.overrides.auto.offset.reset = latest


consumer.overrides.auto.offset.reset = latest
consumer.auto.offset.reset = latest
auto.offset.reset = latest
