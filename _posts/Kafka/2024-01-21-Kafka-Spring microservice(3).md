---
title: '3. Kafka Consumer config'
excerpt: 'Spring kafka Consumer'

categories:
    - Kafka
tags:
    - [Spring, Kafka]

permalink: /Kafka/3

toc: true
toc_sticky: true

date: 2024-01-21
last_modified_at: 2024-01-21
---

## 1. kafka Consumer config

Consumer config

```java
public class KafkaConsumerConfig {

    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    public Map<String, Object> consumerConfig() {
        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return props;

    }

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        return new DefaultKafkaConsumerFactory<>(consumerConfig());
    }

    public KafkaListenerContainerFactory<
            ConcurrentMessageListenerContainer<String, String>
            > factory(
                    ConsumerFactory<String, String> consumerFactory
    ) {
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory);
        return factory;
    }

}
```

## 2. Kafka Listener

KafkaListener.class

```java
@Component
public class KafkaListeners {

    @KafkaListener(topics = "test",
    groupId = "groupId")
    void listener(String data) {
        System.out.println("received message: " + data);
    }
}

```

## 3. message Controller

MessageController.class

```java
@RequiredArgsConstructor
@RestController
@RequestMapping("api/v1/messages")
public class MessageController {

    private final KafkaTemplate<String, String> kafkaTemplate;

    @PostMapping
    public void publish(@RequestBody MessageRequest request) {
        kafkaTemplate.send("test",request.message());
    }
}
```

<!-- <img src="/assets/images/Kafka/1-4.png"> -->
