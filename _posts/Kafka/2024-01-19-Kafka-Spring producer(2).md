---
title: '1.Spring으로 Kafka Producer 설정'
excerpt: 'Spring kafka producer'

categories:
    - Kafka
tags:
    - [Spring, Kafka]

permalink: /Kafka/2

toc: true
toc_sticky: true

date: 2024-01-18
last_modified_at: 2024-01-18
---

## 1. kafka server-properties config

kafka container 포트 입력한다.

```properties
# application.properties

# kafka 포트 정보
spring.kafka.bootstrap-servers = localhost:9092
```

## 2. kafka Topic config

자바 코드로 토픽을 생성한다.

```java
// KafkaTopicConfig.java

@Configuration
public class KafkaTopicConfig {

    @Bean
    public NewTopic testTopic() {
        // test 토픽 생성
        return TopicBuilder.name("test")
                .build();
    }

}

```

## 3. kafka producer config

producer를 담당하는 kafkaTemplate를 생성하기 위해 각 설정을 입력한다.

```java
@Configuration
public class KafkaProducerConfig {


    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    // producer 설정 입력
    public Map<String, Object> producerConfig() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return props;


    }

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        return new DefaultKafkaProducerFactory<>(producerConfig());
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate(
            ProducerFactory<String, String> producerFactory
    ) {
        // kafka Template 반환(핵심)
        return new KafkaTemplate<>(producerFactory);
    }
}
```

모든 설정이 완료되었으면 데이터를 생성된 토픽에 전송 테스트

```java
@SpringBootApplication
public class KafkaTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(KafkaTestApplication.class, args);
    }

    @Bean
    CommandLineRunner commandLineRunner(KafkaTemplate<String, String> kafkaTemplate) {
        // kafkaTemplate는 KafkaProducerConfig의 bean을 가져옴
        return args -> {
          kafkaTemplate.send("test", "hello kafka");
        };
    }
}
```

<!-- <img src="/assets/images/Kafka/1-4.png"> -->
