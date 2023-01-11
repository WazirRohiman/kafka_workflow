-- Create a topic in this case the topic created is 'bankbranch' (in a new terminal) to process messages coming from atm machines of bank branches
-- Here we will be creating partitions as well, in this case 2 partitions
```
cd kafka_2.12-2.8.0
bin/kafka-topics.sh --bootstrap-server localhost:9092 --create --topic bankbranch  --partitions 2
```

-- You can list the topic to see if the topic was created correctly
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

-- Or you can use ```describe``` command to check the details of the topic 
```
bin/kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic bankbranch
```

-- We can create a producer topic. Once ```>``` appears you can start writing the messages
```
bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic bankbranch 
```

-- Writing some messages to the topic (with the producer) in this case a simple JSON object with an atm id and transaction id (each written on a new line after >)
```
{"atmid": 1, "transid": 100}
{"atmid": 1, "transid": 101}
{"atmid": 2, "transid": 200}
{"atmid": 1, "transid": 102}
{"atmid": 2, "transid": 201}
```

-- Next create a consumer to consume the above messages (in a new terminal)
```
cd kafka_2.12-2.8.0
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --from-beginning
```
**Note that no message key was used, so the messages may not appear in order to achieve
a message key will be used later**

---------------------------------------------------------

Now we'll be using message keys to ensure that messages with the same key will be consumed in the same order as they were published. In the backend, messages with the same key will be published into the same partition and will always be consumed by the same consumer. As such, the original publication order is kept in the consumer side.

**But first stop the current consumer and current producer using the following command in each terminal in the following order**

1. In consumer terminal
```
CTRL+C
```
2. In producer terminal
```
CTRL+C
```

---------------------------------------------------------

Now we need to start a new producer by adding the message key command ```--property parse.key=true``` to make the producer pass message keys
```--property key.separator=:``` defines the key seperator to be ```:```
So the new JSON object will look something like this ```1:{"atmid": 1, "transid": 102}```

-- Producer with partitions 
```bin/kafka-console-producer.sh --bootstrap-server localhost:9092 --topic bankbranch --property parse.key=true --property key.separator=:```

-- Messages with message key
```
1:{"atmid": 1, "transid": 102}
1:{"atmid": 1, "transid": 103}
2:{"atmid": 2, "transid": 202}
2:{"atmid": 2, "transid": 203}
1:{"atmid": 1, "transid": 104}
```

-- Consumer with property arguments
```bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch --from-beginning --property print.key=true --property key.separator=:```

----------------------------------------------------------------------------------------

Now, you should see that messages that have the same key are being consumed in the same order (e.g., ```trans102 -> trans103 -> trans104```) as they were published.

This is because each topic partition maintains its own message queue, and new messages are enqueued (appended to the end of the queue)
as they get published to the partition. Once consumed, the earliest messages will be dequeued and nno longer be available for consumption.

Recall that with two partitions and no message keys specified, the transaction messages were published to the two partitions
in rotation:

    ```Partition 0: [{"atmid": 1, "transid": 102}, {"atmid": 2, "transid": 202}, {"atmid": 1, "transid": 104}]````
   ```Partition 1: [{"atmid": 1, "transid": 103}, {"atmid": 2, "transid": 203}]```

As you can see, the transaction messages from atm1 and atm2 got scattered across both partitions. It would be difficult
to unravel this and consume messages from one ATM with the same order as they were published.

However, with message key specified as the atmid value, the messages from the two ATMs will look like the following:

    ```Partition 0: [{"atmid": 1, "transid": 102}, {"atmid": 1, "transid": 103}, {"atmid": 1, "transid": 104}]```
    ```Partition 1: [{"atmid": 2, "transid": 202}, {"atmid": 2, "transid": 203}]```

Messages with the same key will always be published to the same partition, so that their published order will be preserved within the message queue of each partition.

As such, we can keep the states or orders of the transactions for each ATM.




