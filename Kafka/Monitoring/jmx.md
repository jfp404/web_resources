- (https://aws-labs.com/kafka-jmx-with-ssl-and-user-password-authentication/)
- (https://docs.oracle.com/javadb/10.10.1.2/adminguide/radminjmxenabledisable.html)
- (https://www.ibm.com/docs/en/elm/6.0.2?topic=ejat-option-setting-up-jmx-client-authentication-without-ssl)
- (https://wiki.scn.sap.com/wiki/display/BOBJ/Troubleshooting+failed+JMX+connections+with+JConsole)

```
jconsole.exe -debug -J-Djava.util.logging.config.file=./logging.properties service:jmx:rmi:///jndi/rmi://10.9.79.48:9502/jmxrmi
```
JMXTERM
- (https://rmoff.net/2018/09/19/exploring-jmx-with-jmxterm/)


## Monitoring post collection

- DataDog Post (https://www.datadoghq.com/blog/monitoring-kafka-performance-metrics/)
- Instaclustr (https://www.instaclustr.com/support/documentation/kafka/monitoring-information/)

## JMXTOOL
- StackOverFlow, how to use jmxtool (https://stackoverflow.com/questions/36708384/how-to-enable-remote-jmx-on-kafka-brokers-for-jmxtool)
- (https://jaceklaskowski.gitbooks.io/apache-kafka/content/kafka-tools-JmxTool.html)
- 


## Understanding Kafka

- The internals of kafka (https://jaceklaskowski.gitbooks.io/apache-kafka/content/)
  - Good read to understand Kafka
  
## Mirror maker monitoring
- (https://github.com/apache/kafka/tree/trunk/connect/mirror)