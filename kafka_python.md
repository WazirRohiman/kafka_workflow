** This is a concise overview of the kafka-python client

**kafkaAdminClient Class**

To use ```KafkaAdminClient```, we first need to define and create a ```KafkaAdminClient``` object:
```
admin_client = KafkaAdminClient(bootstrap_servers="localhost:9092", client_id='test')
```

**Create new topics**

1. Define empty topic lis

```
new_topic = []
```

2. Use NewTopic class to create a new topic

```
new_topic = NewTopic(name="bankbranch", num_partitions= 2, replication_factor=1)
topic_list.append(new_topic)
```

3. Use create_topics() method to create new topics
```
admin_client.create_topics(new_topics=topic_list)
```

--------------------------------------------------------------------

** Describe topic

Check topic configuration details using ```decribe_configs()``` method

```
configs = admin_client.describe_configs(config_resources=[ConfigResource(ConfigResourceType.TOPIC, "bankbranch")])
```
The above operations is equivalent to using the kafka CLI client terminal code
```
kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic bankbranch
```

-----------------------------------------------------------------------

** Kafka Producer

1. First define and create a kafka producer using the KafkaProducer class. We need to encode and serialize JSON messages int bytes
```
producer = KafkaProducer(value_serializer=lambda v: json.dumps(v).encode('utf-8'))
```
For the ```value_serializer``` argument, we define a lambda function to take a Python dict/list object and
serialize it into bytes.

2. Produce messages in JSON format as follows from the producer object
```
producer.send("bankbranch", {'atmid':1, 'transid':100})
```
```
producer.send("bankbranch", {'atmid':2, 'transid':101})
```

The above is equivalent to using the following command in a Kafka CLI client

```
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic bankbranch
```

--------------------------------------------------------------------------------

** Kafka Consummer

Use KafkaConsummer class to consume published messages

1. Define and create a consume variable with the KafkaConsume class subscribing to the desired topic as follows

```
consumer = KafkaConsumer('bankbranch')
```

2. Once created use a for loop to iterate and print all messages saved to consumer

```
for msg in consumer:
    print(msg.value.decode("utf-8"))
```

The above is the same as using the following command in kafka client CLI

```
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic bankbranch
```




