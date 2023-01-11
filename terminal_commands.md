-- Download Kafka, by running the command below:
```
wget https://archive.apache.org/dist/kafka/2.8.0/kafka_2.12-2.8.0.tgz
```
-- Extract kafka from the zip file by running the command below - this will create a new directory
```
tar -xzf kafka_2.12-2.8.0.tgz
```
-- ZooKeeper is required for Kafka to work. Start the ZooKeeper server.
```
cd kafka_2.12-2.8.0
bin/zookeeper-server-start.sh config/zookeeper.properties
```

-- ZooKeeper, as of this version, is required for Kafka to work. 
-- ZooKeeper is responsible for the overall management of Kafka cluster. 
-- It monitors the Kafka brokers and notifies Kafka if any broker or partition goes down, or if a new broker or partition goes up.

-- Start kafka messsage broker service (in a new terminal)
```
cd kafka_2.12-2.8.0
bin/kafka-server-start.sh config/server.properties
```
-- Create a topic *in this case the topic created is 'news'* (in a new terminal)
```
cd kafka_2.12-2.8.0
bin/kafka-topics.sh --create --topic news --bootstrap-server localhost:9092
```
-- You need a producer to send messages to kafka
-- First start a producer using the producer shell script
```
bin/kafka-console-producer.sh --topic news --bootstrap-server localhost:9092
```

-- Once the producer starts, and you get the ‘>’ prompt, type any text message and press enter

Type any message

-- Now you need consumers to read a messsage on kafka - create the consumers using consumer shell script in the new terminal
-- In the case below we are consuming from the topic 'news' and we are reading all message from the beginning (i.e no offset) 
-- As soon as the script runs the message should appear

```
cd kafka_2.12-2.8.0
bin/kafka-console-consumer.sh --topic news --from-beginning --bootstrap-server localhost:9092
```

--------------------------------------


Kafka uses the directory ``` /tmp/kakfa-logs to store the messages.```

Explore the folder news-0 inside ```/tmp/kakfa-logs.```

This is where all the messages are stored.

Explore the folder ```/home/project/kafka_2.12-2.8.0```

**This folder has the below 3 sub directories.**
Directory 	Contents
bin 	      shell scripts to control kafka and zookeeper
config 	    configuration files
logs 	      log files forkafka and zookeeper
