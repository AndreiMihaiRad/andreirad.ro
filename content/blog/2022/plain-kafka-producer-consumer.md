---
title: "Plain Kafka Producer Consumer"
date: 2022-10-04T18:56:14+03:00
draft: false
tags: ["kafka", "java"]
---

## Setting Up Plain Kafka consumer and producers in Java

This article aims to help anyone who wants to start with Kafka but does not want to use any Java Framework.
We will cover all the steps needed for you to setup a Kafaka Broker as well as configure a basic gradle project with both Producers and Consumers.

![](/images/2022/Kafka%20Consumer%20Producer.drawio.png)

What you will learn:

- Install Kafka on the Docker container.
- Create a Kafka producer to emit a message to a broker.
- Create a Kafka consumer to listen for messages on a specified topic.
- Run the Application from the command line

## Prerequisites

All the steps in this article were executed on macOS Monterey v 12.6. The commands are pretty basic and should be very similar for Windows or Linux.

### Dependencies

- Java 11 or greater
- Docker
- Gradle 7.5.1

Installing these tools does not represent the purpose of this article, please visit the official websites for further details.

### Setting Up Gradle Project

- Create new Gradle project

```bash {linenos=yes}
# create new project folder 
mkdir plain-kafka-producer-consumer

# enter newly created folder and run following gradle command
gradle init

Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4] 2

Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Scala
  6: Swift
Enter selection (default: Java) [1..6] 3

Split functionality across multiple subprojects?:
  1: no - only one application project
  2: yes - application and library projects
Enter selection (default: no - only one application project) [1..2] 1

Select build script DSL:
  1: Groovy
  2: Kotlin
Enter selection (default: Groovy) [1..2] 1

Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no] no
Select test framework:
  1: JUnit 4
  2: TestNG
  3: Spock
  4: JUnit Jupiter
Enter selection (default: JUnit Jupiter) [1..4] 2

Project name (default: plain-kafka-producer-consumer):
Source package (default: plain.kafka.producer.consumer): com.example.kafka
```

- Open the project in IntelliJ
- Add dependencies that we will use later in project, update ```build.gradle``` as follows
  
```groovy {linenos=yes}
    plugins {
    id 'application'
    id 'java'
}

ext.javaMainClass = "com.example.App"

application {
    mainClassName = javaMainClass
}

repositories {
    // Use Maven Central for resolving dependencies.
    mavenCentral()
}

dependencies {
    // Lombok
    compileOnly 'org.projectlombok:lombok:1.18.24'
    annotationProcessor 'org.projectlombok:lombok:1.18.24'

    // Kafka Client
    implementation 'org.apache.kafka:kafka-clients:3.3.1'

    //Jackson
    implementation "com.fasterxml.jackson.core:jackson-core:2.13.4"
    implementation "com.fasterxml.jackson.core:jackson-databind:2.13.4"

    // Logging
    implementation 'org.slf4j:slf4j-api:2.0.3'
    implementation 'org.slf4j:slf4j-log4j12:2.0.3'
    implementation 'org.apache.logging.log4j:log4j:2.19.0'

    // Use TestNG framework, also requires calling test.useTestNG() below
    testImplementation 'org.testng:testng:7.4.0'
}

tasks.named('test') {
    // Use TestNG for unit tests.
    useTestNG()
}
```

- Now let's add a configuration file for log4j. Here we have just a basic example of configuration just to be able to log on to stdout.

``` {linenos=yes}
# Root logger option
log4j.rootLogger=INFO, stdout

# Direct log messages to stdout
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
```

- Also create a new file called ```config.properties``` and fill it with properties needed for Kafka.
There much more config options, but for now these are the minimal configs we need.
For all the options, just check the [official documentation](https://kafka.apache.org/documentation/)

``` {linenos=yes}
# The location of the Kafka server
bootstrap.servers=localhost:9092

# the default group ID
group.id=test-group

# The number of records to pull of the stream every time the client takes a trip out to Kafka
max.poll.records=10

# Make Kafka keep track of record reads by the consumer
enable.auto.commit=true

# The time in milliseconds to Kafka write the offset of the last message read
auto.commit.interval.ms=500
```

Alright, so we are pretty much ready for some coding.

Our Demo Application will have two important parts. A Producer one and a Consumer.

The final goal is to be able to use the command line to start our application in producer or consumer mode and be able to see in our logs that events are produced or consumed.

Also (as most modern applications use JSON Objects) we want to produce/consume event payload as JsonObjects.

First, let's create a dummy event object.

```java {linenos=yes}
@Data
@AllArgsConstructor
@NoArgsConstructor
public class UserEvent {
    private String firstName;
    private String lastName;
}
```

Because the Kafka-client library provides us with just ```StringSerializer``` and ```StringDeserializer``` we have to implement our logic to handle the event payload.

```java {linenos=yes}
@Slf4j
public class JsonDeserializer<T> implements Deserializer<T> {

    private final ObjectMapper objectMapper;
    private final Class<T> toClazz;

    public JsonDeserializer(final Class<T> toClazz) {
        this.objectMapper = new ObjectMapper();
        this.objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
        this.toClazz = toClazz;
    }

    @Override
    public T deserialize(String topic, byte[] data) {
        try {
            return this.objectMapper.readValue(data, toClazz);
        } catch (IOException e) {
            log.error("Message could not be deserialize", e);
            throw new SerializationException(e);
        }
    }
```

```java {linenos=yes}
@Slf4j
public class JsonSerializer implements Serializer{

    private final ObjectMapper objectMapper;

    public JsonSerializer() {
        this.objectMapper = new ObjectMapper();
    }

    @Override
    public byte[] serialize(String topic, Object data) {
        try {
            return objectMapper.writeValueAsBytes(data);
        } catch (JsonProcessingException e) {
            log.error("Message could not be deserialize", e);
            throw new SerializationException(e);
        }
    }
}
```

These classes will come in handy when we'll create ```KafkaProducer``` and ```KafkaConsumer```

To keep our code organized we will create a dedicated class that will be responsible for creating ```KafkaProducer``` and ```KafkaConsumer```.
Here you can observe that properties are loaded from the file we've created before.

```java {linenos=yes}
@UtilityClass
public class KafkaFactory {

    public <T>KafkaConsumer<String, T> createConsumer(final Class<T> payloadType) {
        Properties props = loadProperties();
        return new KafkaConsumer<>(props, new StringDeserializer(), new JsonDeserializer<>(payloadType));
    }

    public KafkaProducer createProducer() {
        Properties props = loadProperties();
        return new KafkaProducer<>(props,  new StringSerializer(), new JsonSerializer());
    }

    private Properties loadProperties() {
        try (InputStream input = KafkaFactory.class.getClassLoader().getResourceAsStream("config.properties")) {
            Properties props = new Properties();
            if (input == null) {
                throw new RuntimeException("Unable to load configuration");
            }
            props.load(input);
            return props;
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

Now we will create the classes that will handle the logic of starting/stopping, and handling messages for both scenarios (consuming/producing messages).
For this purpose I considered it a good idea to create two Abstract generic classes where we can have all common code that can be reused for all the topics and event types we might have.

The class that encapsulates the Kafka producer is named AbstractKafkaProducer. The class that encapsulates the Kafka consumer is named AbstractKafkaConsumer. Each one contains specialized methods to handle consumer records, as well as stopping and starting the threads.

```java {linenos=yes}
@Slf4j
public abstract class AbstractKafkaConsumer<V> {
    private final int TIME_OUT_MS = 5000;
    private final KafkaConsumer<String, V> consumer;
    private final String topic;
    private final AtomicBoolean closed = new AtomicBoolean(false);


    protected AbstractKafkaConsumer(final KafkaConsumer<String, V> consumer, final String topic) {
        this.consumer = consumer;
        this.topic = topic;
        addShutdownHook();
    }

    public void runAlways() throws Exception {

        //keep running forever or until shutdown() is called from another thread.
        try {
            this.consumer.subscribe(Collections.singletonList(topic));
            while (!closed.get()) {
                ConsumerRecords<String, V> records = this.consumer.poll(Duration.ofMillis(TIME_OUT_MS));
                if (records.count() == 0) {
                    log.info("No records retrieved");
                }

                for (ConsumerRecord<String, V> record : records) {
                    recordHandler(record);
                }
            }
        } catch (WakeupException e) {
            // Ignore exception if closing
            if (!closed.get()) throw e;

        }
    }

    protected abstract void recordHandler(ConsumerRecord<String, V> record);

    public void shutdown() {
        closed.set(true);
        log.info("Shutting down consumer");
        this.consumer.wakeup();
    }

    private void addShutdownHook() {
        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                shutdown();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }));
        log.info("Created the Shutdown Hook");
    }
}
```

```java {linenos=yes}
@Slf4j
public abstract class AbstractKafkaProducer<V> {

    private final KafkaProducer<String, V> producer;
    private final String topic;

    private final AtomicBoolean closed = new AtomicBoolean(false);

    protected AbstractKafkaProducer(KafkaProducer<String, V> kafkaProducer, String topic) {
        this.producer = kafkaProducer;
        this.topic = topic;
        addShutdownHook();
    }

    public void runAlways() throws Exception {
        while (true) {
            log.info("Producing new event ...");
            String key = UUID.randomUUID().toString();
            V payload = getRandomPayloadObject();

            ProducerRecord<String, V> producerRecord = new ProducerRecord<>(this.topic, key, payload);
            this.producer.send(producerRecord);
            Thread.sleep(3000);
        }
    }

    public void shutdown() throws Exception {
        closed.set(true);
        log.info("Shutting down producer");
        this.producer.close();
    }

    private void addShutdownHook() {
        Runtime.getRuntime().addShutdownHook(new Thread() {
            @Override
            public void run() {
                try {
                    shutdown();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        log.info("Created the Shutdown Hook");
    }

    public abstract V getRandomPayloadObject();
}
```

Still here? It's time to create some concrete classes. Very very simple.

```java {linenos=yes}
@Slf4j
public class UserEventKafkaConsumers extends AbstractKafkaConsumer<UserEvent> {

    public UserEventKafkaConsumers() {
        super(KafkaFactory.createConsumer(UserEvent.class), KafkaTopics.USER_TOPIC.getValue());
    }

    @Override
    protected void recordHandler(ConsumerRecord<String, UserEvent> record) {
        log.info("UserEvent record handler");
        log.info("Event with key:{} and value:{} received", record.key(), record.value());
    }
}
```

```java {linenos=yes}
@Slf4j
public class UserEventKafkaProducer extends AbstractKafkaProducer<UserEvent> {

    public UserEventKafkaProducer() {
        super(KafkaFactory.createProducer(), KafkaTopics.USER_TOPIC.getValue());
    }

    @Override
    public UserEvent getRandomPayloadObject() {
        String firstName = MessageHelper.getRandomString();
        String lastName = MessageHelper.getRandomString();
        UserEvent userEvent = new UserEvent(firstName, lastName);
        log.info("UserEvent was produced");
        return userEvent;
    }
}
```

As the names imply, UserEventKafkaProducer emits messages to the Kafka broker, and those messages are retrieved and processed by UserEventKafkaConsumers.

Good, one more thing to do.
The demonstration project publishes an ```App``` class that is the starting point of execution. This class has a ```main()``` method that starts with either a Kafka producer or a Kafka consumer. Which one it starts will depend on the values passed as parameters to main() at the command line.

```java {linenos=yes}
@Slf4j
public class App {

    public static void main(String[] args) throws Exception {
        String mode = args[0];
        switch(mode.toLowerCase(Locale.ROOT)) {
            case "producer":
                log.info("Starting the Producer\n");
                UserEventKafkaProducer userProducer = new UserEventKafkaProducer();
                userProducer.runAlways();
                break;
            case "consumer":
                log.info("Starting the Consumer\n");
                UserEventKafkaConsumers userConsumer = new UserEventKafkaConsumers();
                userConsumer.runAlways();
                break;
            default:
                log.error("Mode:{} is not supported", mode);
        }
    }
}
```

## Testing

### Getting Kafka up and running with Docker

Execute command below to spin up a kafka broker.

```bash {linenos=yes}
docker run \
    -it \
    --name kafka-broker \
    -p 9092:9092 \
    -e LOG_DIR=/tmp/logs quay.io/strimzi/kafka:latest-kafka-3.2.1-amd64 \
    /bin/sh -c 'export CLUSTER_ID=$(bin/kafka-storage.sh random-uuid) && bin/kafka-storage.sh format -t $CLUSTER_ID -c config/kraft/server.properties && bin/kafka-server-start.sh config/kraft/server.properties'
```

### Start Producer

```bash {linenos=yes}
./gradlew run --args="producer"
```

### Start Consumer

```bash {linenos=yes}
./gradlew run --args="consumer"
```

## Conclusions

Kafka is a very powerful tool, widely used by many organizations.
In most cases, we don't have to use plain java implementation. Spring have o good support for Kafka integration and you can have a quicker implementation using Spring.
But there are situations where you have to just debug something, implement, and e2e test outside your micro-service and you maybe don't have any framework to support you.
I hope this example will help you and be a starting point for what you have to achieve.

### All the examples posted in this article can be found on [GitHub](https://github.com/AndreiMihaiRad/plain-kafka-producer-consumer)
