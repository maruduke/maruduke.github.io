---
title: '\[Kafka\] 2. Kafka Consumer config'
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

Producer과 마찬가지로 Consumer에 적용할 config 파일을 생성한다.

```java
// KafkaConsumerConfig.java
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

producer가 특정 토픽에 전송한 데이터를 받기 위해 @KafkaListener 어노테이션을 사용

```java
// KafkaListener.java
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

메시지 컨트롤러를 통하여 api를 통해 kafka메시지를 전송하고,
kafkaListeners를 통해 전송 받은 데이터 확인한다.

```java
// MessageController.java
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
