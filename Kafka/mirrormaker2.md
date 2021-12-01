# Kafka Cluster migrations
## Active -> Passive pattern

### dale lane blog (must check)
- (https://dalelane.co.uk/blog/?p=4303)


## Set up MM2
- (https://medium.com/larus-team/how-to-setup-mirrormaker-2-0-on-apache-kafka-multi-cluster-environment-87712d7997a4)
- (https://ibm-cloud-architecture.github.io/refarch-eda/technology/kafka-mirrormaker)
- (https://towardsdatascience.com/10-configs-to-make-your-kafka-producer-more-resilient-ec6903c63e3f)
- (https://www.cloudkarafka.com/blog/kafka-producer-side-settings-explained-linger-ms-and-batch-size.html)
- 

## Tunning
- (https://engineering.salesforce.com/mirrormaker-performance-tuning-63afaed12c21)
- (https://wmclane.medium.com/how-to-optimize-mirrormaker2-for-high-performance-apache-kafka-replication-697bc5089c64)
- (https://towardsdatascience.com/spin-up-new-mirrormaker-in-5-minutes-a28c14bcde9f)

##IBM mirror maker
- (https://ibm-cloud-architecture.github.io/refarch-eda/technology/kafka-mirrormaker/)
- 
- (https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)

##NEW RELIC RECOMMENDATIONS - INTERESTING
- (https://newrelic.com/blog/best-practices/kafka-best-practices#:~:text=Tune%20your%20consumer%20socket%20buffers,bytes%20%2C%20which%20defaults%20to%2064kB.)
  



## Other sources
- (https://dzone.com/articles/kafka-replication-manipulate-your-topics-and-recor)
- (https://dalelane.co.uk/blog/?p=4074)


##StackOverflow
- (https://stackoverflow.com/questions/59390555/is-it-possible-to-replicate-kafka-topics-without-alias-prefix-with-mirrormaker2)


##MM performance
- (https://medium.com/metrosystemsro/apache-kafka-how-to-test-performance-for-clients-configured-with-ssl-encryption-3356d3a0d52b)

- MirrorMaker not renamming 
  - set up this conf in mm2
    ```
    clusters = ot2dev, bp2ot2devlab
    ot2dev.bootstrap.servers = 10.9.202.17:9092,10.9.202.17:9092,10.9.200.120:9092,10.9.200.120:9092
    bp2ot2devlab.bootstrap.servers = bp2-vot2kb5-l01.bksvc.otxlab.net:9092,bp2-vot2kb5-l02.bksvc.otxlab.net:9092,bp2-vot2kb5-l03.bksvc.otxlab.net:9092
    ot2dev->bp2ot2devlab.enabled = True
    ot2dev->bp2ot2devlab.topics = audit-migration-connector-nashua-configs,ot2-appevents-admincenterdevspace-dekms
    replication.factor = 3
    replication.policy.separator=
    source.cluster.alias=
    target.cluster.alias=
    ssl.client.auth = required
    bp2ot2devlab.security.protocol = SSL
    bp2ot2devlab.ssl.truststore.location = /data1/conf/kafka/ssl/kafka_connect.truststore.jks
    bp2ot2devlab.ssl.truststore.password = a3cab903b83b2d669f352a927df77ec5
    bp2ot2devlab.ssl.keystore.location = /data1/conf/kafka/ssl/kafka_connect.keystore.jks
    bp2ot2devlab.ssl.keystore.password = 5e6f8a40a3424255abf76a22e308ae8b
    ot2dev.security.protocol = PLAINTEXT
    #ot2dev.ssl.truststore.location = /data1/conf/kafka/ssl/kafka_connect.truststore.jks
    #ot2dev.ssl.truststore.password = a3cab903b83b2d669f352a927df77ec5
    #ot2dev.ssl.keystore.location = /data1/conf/kafka/ssl/kafka_connect.keystore.jks
    #ot2dev.ssl.keystore.password = 5e6f8a40a3424255abf76a22e308ae8b
    ```


##EXACTLY ONCE DELIVER
- (https://cwiki.apache.org/confluence/display/KAFKA/KIP-656%3A+MirrorMaker2+Exactly-once+Semantics)
- (https://towardsdatascience.com/exactly-once-semantics-across-multiple-kafka-instances-is-possible-20bf900c29cf)
- (https://github.com/apache/kafka/pull/9451)
- https://github.com/ning2008wisc/kafka-producer-consumer-test
- (https://docs.google.com/document/d/11Jqy_GjUGtdXJK94XGsEIK7CP1SnQGdp2eF0wSw9ra8/edit)