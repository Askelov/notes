# Kafka in action- Dylan D. Scott

my notes:

##vWhat is Kafka?
The Apache Kafka site defines it as a distributed streaming kafka.apache.org/intro platform that has three main capabilites:
 Provide the ability to publish/subscribe to records like a message queue, 
 store records with fault-tolerance,
 and process streams as they occur.
 
 flow : produces - kafka - consumer
 
 Kafka message delivery can take at least the following three delivery methods:
At least once semantics 
At most once semantics 
Exactly once semantics

Kafka’s default guarantee is at least once semantics. This means that Kafka can be configured to allow for a producer of messages to send the same message more than once and have it written to the brokers. When a message has not received a guarantee that it was written to the broker, the producer can send the message again in order to try again. For those cases where you can’t miss a message, say that someone has paid an invoice, this guarantee might take some filtering on the consumer end, but is one of the safest methods for delivery.

Big difference between kafka and other messages brokes
Kafka was designed to have multiple consumers. 
 Kafka allows for parallel processing of data and can have multiple consumers on the same exact topic

 When Kafka might not be the right fit?
 What if you only need a once monthly or even once yearly summary of aggregate data? If you don’t need an on-demand view, quick answer, or even the ability to reprocess data, then you might not need Kafka running throughout the entire year for that task alone. 
 If your main access pattern for data is mostly random lookup of data, Kafka might not be your best option.
 
START
 
Component		 Role
Producer		 Sends message to Kafka
Consumer 		 Retrieves messages from Kafka
Topics 		     Logical name of where message are stored in the broker
ZooKeeper ensemble 		Helps maintain consensus in the cluster
Broker 			Handles the commit log

A Producer is the way to send messages into Kafka
A Consumer is the way to retrieve messages from Kafka.
Topics are where most users will start to think about the logic of what messages should go where. Topics are actually made out of units called partitions. In other words, one or many partitions can make up a single topic 

-ZooKeeper
-COMMIT LOG
-kafka streams/connect/adminclient/ksql packages 


producer:
Properties props = new Properties();                                                   props.put("bootstrap.servers", "localhost:9092,localhost:9093");                       
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer"); props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
Producer<String, String> producer = new KafkaProducer<>(props);                        
ProducerRecord producerRecord = new ProducerRecord<String, String> ("helloworld", null, "hello world again!");                                            
producer.send(producerRecord);                                                         
producer.close(); 
The producer takes a map of name/value items to configure its various options.
This property can take a list of message brokers.
The key and value of the message have to be told what format they will be serializing.
This creates a producer instance. Producers are thread-safe!
This is what represents our message.
Sending the record to the message broker!
Cleaning up before we shut it all down.
consumer:
Properties props = new Properties();                               props.put("bootstrap.servers", "localhost:9092,localhost:9093"); props.put("group.id", "helloconsumer"); props.put("enable.auto.commit", "true"); props.put("auto.commit.interval.ms", "1000"); props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer"); props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
 KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props); consumer.subscribe(Arrays.asList("helloworld"));                  
 while (true) {     ConsumerRecords<String, String> records = consumer.poll(100);      for (ConsumerRecord<String, String> record : records)                  System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value()); }

Properties are set the same way as producers.
The consumer needs to tell Kafka what topics it is interested in.
An infinite loop! But we want to keep polling for new messages as they come in.
We are printing out each record that we consume to the console to see the result.




