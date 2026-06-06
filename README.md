# spring-kafka-study

Spring for Apache Kafka를 학습하기 위한 프로젝트입니다. 메시지 발행(Producer)과
구독(Consumer)을 구성하고, 임베디드 Kafka로 테스트하는 흐름을 실습합니다.

## 학습 목적

Spring Kafka의 `KafkaTemplate`/`@KafkaListener` 기반 메시지 송수신을 구성하고,
`@EmbeddedKafka`를 이용해 브로커 없이 통합 테스트하는 방법을 익힙니다.

## 사용 기술

- Java 17
- Spring Boot 3.2.2
- Spring Boot Starter Web
- Spring Boot Starter Data JPA
- Spring for Apache Kafka (`spring-kafka`)
- QueryDSL 5.0.0 (jakarta) — `com.ewerk.gradle.plugins.querydsl` 1.0.10
- H2 (인메모리 DB)
- Lombok
- Gradle (Groovy DSL)
- 테스트: `spring-kafka-test`, `spring-boot-starter-test`

## 다룬 주제 (코드 근거)

- **Producer** (`KafkaSampleProducerService`, `KafkaProducer`)
  - `KafkaTemplate<String, Object>`로 `oingdaddy` 토픽에 메시지 발행.
  - REST 트리거: `KafkaSampleProducerController`.
- **Consumer** (`KafkaSampleConsumerService`, `KafkaConsumer`)
  - `@KafkaListener(topics = "oingdaddy", groupId = "group-id-oing")`로 메시지 수신.
- **Kafka 설정** (`config/KafkaProducerConfig`, `KafkaConsumerConfig`, `KafkaReceiveConfiguration`)
  - `application.yml`에서 bootstrap-servers(`localhost:9092`), String 직렬화/역직렬화,
    `auto-offset-reset: earliest`, `group-id` 등 설정.
- **임베디드 Kafka 테스트** (`KafkaConsumerTest`)
  - `@EmbeddedKafka(partitions = 1, ports = {9092})`로 브로커를 띄워 `DemoViewDTO` 페이로드
    송수신을 검증.
- **DTO** (`config/DemoViewDTO`) — Lombok `@Builder` 기반 메시지 페이로드.

> JPA + QueryDSL + H2 의존성이 선언되어 있으나(application.yml에 datasource/jpa 설정 존재),
> Kafka 메시지 송수신이 학습의 핵심입니다. 엔티티/QueryDSL 사용 코드는 현재 트리에서 확인되지 않습니다.

## 설정

- 애플리케이션 포트: `8016`
- Kafka 브로커: `localhost:9092` (로컬 실행 필요, 테스트는 임베디드 Kafka 사용)

## 프로젝트 구조

```
spring-kafka-study/
├── build.gradle
└── src
    ├── main/java/com/study/springkafkastudy
    │   ├── SpringKafkaStudyApplication.java
    │   ├── KafkaSampleProducerService.java   # KafkaTemplate 발행
    │   ├── KafkaSampleConsumerService.java   # @KafkaListener 수신
    │   ├── config/                           # Producer/Consumer Config, DemoViewDTO
    │   └── controller/KafkaSampleProducerController.java
    ├── main/resources/application.yml
    └── test/java/com/study/springkafkastudy
        ├── KafkaConsumerTest.java            # @EmbeddedKafka 통합 테스트
        └── SpringKafkaStudyApplicationTests.java
```
