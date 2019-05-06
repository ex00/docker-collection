# kafka

`docker-compose up -d`

## check
`docker attach kafka_consumer_1`

output:
```
[2019-05-06 13:48:49,893] WARN [Consumer clientId=consumer-1, groupId=console-consumer-87191] Error while fetching metadata with correlation id 3 : {my_topic=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)      
[2019-05-06 13:48:50,468] WARN [Consumer clientId=consumer-1, groupId=console-consumer-87191] Error while fetching metadata with correlation id 8 : {my_topic=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)      
[2019-05-06 13:48:51,898] WARN [Consumer clientId=consumer-1, groupId=console-consumer-87191] Error while fetching metadata with correlation id 11 : {my_topic=LEADER_NOT_AVAILABLE} (org.apache.kafka.clients.NetworkClient)     
Mon May  6 13:49:44 UTC 2019                                                                                                                                                                                                      
Mon May  6 13:49:53 UTC 2019                                                                                                                                                                                                      
Mon May  6 13:49:56 UTC 2019  
```
