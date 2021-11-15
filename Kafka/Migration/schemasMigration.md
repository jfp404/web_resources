- Migration schemas in topics
Schemas are stored in topics called _schemas.

```
bin/kafka-console-consumer --bootstrap-server http://[source] --topic _schemas --from-beginning --property print.key=true --timeout-ms 1000 1> schemas.log


bin/kafka-console-producer --broker-list http://[destination] --topic _schemas --property parse.key=true < schemas.log 
```

##Schema registry deployment
- (https://docs.confluent.io/platform/current/schema-registry/installation/deployment.html)
 
- Backup using command line tools
  ```
  bin/kafka-console-consumer --bootstrap-server localhost:9092 --topic _schemas --from-beginning --property print.key=true --timeout-ms 1000 1> schemas.log
  ```
- Restoring schemas
  ```
  bin/kafka-console-producer --broker-list localhost:9092 --topic _schemas_restore --property parse.key=true < schemas.log
  ```

