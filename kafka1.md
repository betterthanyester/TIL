# Documentation

## 1. Getting Started

## 1.1 Introduction

- ### Event Streaming이란
  - 인간의 중추신경계에 대응됨
  - '항상 켜져있는' 소프트웨어적이고 자동화된 비즈니스가 지배적인 세상에서 기술적 근간이 됨
  - 좀 더 기술적으로 접근해보자.
    - 데이터베이스, 센서, 모바일기기, 클라우드 서비스, 소프트웨어 어플리케이션에서 발생하는 이벤트들의 데이터를 실시간으로 추적
    - 나중에 활용하기 위해 이벤트 스트림을 장기적으로 저장하는 것 : 조작, 처리, 실시간 내지 소급하여 반응
    - 이벤트 스트림을 다른 필요로 하는 기술들에 라우팅
  - 즉 데이터의 연속적인 흐름과 해석을 보장해서 필요한 위치에 필요한 시점에 전달될 수 있도록 한다.
- ### Event Streaming을 활용하는 영역

  - 유스케이스의 예를 들어보면
    - 금융 결제와 거래
    - 운송업계에서 교통 모니터링
    - IoT 장비의 센서 데이터
    - 유통업계에서 고객과의 상호작용 및 주문에 대응
    - 병원에서 환자 상태 모니터링 및 예측
    - 한 회사의 다양한 디비전을 연결
    - 데이터 플랫폼이나 마이크로서비스의 근간

- ### 아파치 카프카가 Event Streaming platform이라는 의미는

  - 카프카는 다음 세가지를 결합한 서비스를 제공한다
    - 읽기/수정
    - 저장
    - 처리
  - 카프카를 셀프 매니징할 수도 있고, 다르 벤더사의 매니지드 서비스를 사용할 수도 있다.

  - 카프카는 다수의 서버와 클라이언트로 구성된 distributed 시스템으로, TCP 프로토콜을 통해 커뮤니케이션

  - 서버
    - 여러 데이터센터와 클라우드 지역으로 확장될 수 있는 서버들의 클러스터로 구성
    - 일부는 broker라는 스토리지 계층을 구성
    - 일부는 데이터베이스 및 다른 카프카 클러스터와 지속적으로 연결하여 이벤트 스트림을 주고 받는 Kafka Connect를 수행
    - 확장이 용이하고 fault-tolerant함 : 하나의 서버가 다운되더라도 다른 서버가 take over해서 데이터 손실없이 지속적인 운영을 가능케 함
  - 클라이언트

- ### 주요 컨셉과 용어

  - 이벤트 (event)

    - 무언가가 발생했다는 것에 대한 기록
    - 레코드(record) 또는 메시지(message)라고도 함
    - 예시
      - Event key: "Alice"
      - Event value: "Made a payment of $200 to Bob"
      - Event timestamp: "Jun. 25, 2020 at 2:06 p.

  - 프로듀서 (producer)
    - 카프카에 이벤트를 작성하는 클라이언트 어플리케이션
  - 컨슈머 (consumer)

    - 카프카의 이벤트를 읽고 처리하는 클라이언트 어플리케이션

  - 프로듀서와 컨슈머는 완전히 분리되어 있고, 서로를 모른다

    - 카프카의 확장성을 가능케하는 요소

  - 토픽 (Topic)

    - 이벤트를 담는 폴더
    - 특정 토픽에 대해 여러 producer와 consumer가 접근할 수 있다
    - 여러번 읽을 수 있다(consume)

      - 토픽별로 보유 기간을 설정할 수 있다 : 이 기간동안은 소비된다고 지워지지 않는다. -카프카의 퍼포먼스는 데이터 크기와 무관하게 일정하다 : 데이터를 오래 보유해도 문제가 없다.

    - 파티션으로 분리되어 있다
      - 데이터들이 서로 다른 broker (스토리지)의 bucket에 담겨있다.
        - 클라이언트 어플리케이션이 다양한 broker에서 데이터를 동시에 읽고 추가할 수 있게 만들어, 확장이 용이하게 만든다.
      - 동일한 키를 가진 이벤트는 동일한 파티션에, 추가된 순서대로 저장된다.
      - fault-tolerant를 위해 데이터를 복제하는데, 이러한 데이터 복제가 토픽의 파티션 레벨에서 진행된다.

- ### 카프카 API
  - Admin API
    - topic, broker 및 기타 카프카 객체를 매니지
  - producer API
    - 한 개 이상의 topic에 이벤트를 작성
  - consumer API
    - 한 개 이상의 topic에서 이벤트를 읽음
  - 카프카 스트림 API
    - input 스트림을 효과적으로 처리해서 output 스트림으로 변환하는 유용한 기능
  - 카프카 connect API
    - 다른 외부 시스템이나 어플리케이션과 연결하여 데이터를 읽고 쓰기 위한 connector를 제공
    - 실제로는 커뮤니티에서 공유가능한 커넥터들이 많기 때문에, 직접 구현할 필요는 없음

## 2. Springboot 와 Kafka 연동 : pub/sub 구현

### 2.1 환경설정

### 1) Kafka 설치

- https://kafka.apache.org/downloads
- kafka 2.12.tgz 다운 -> 원하는 디렉토리에 압축풀기
- scala 버전별로 binary 파일을 제공하는데, 어떤 버전에서도 돌아가게 하려면 scala 2.12를 추천한다

### 2) 프로젝트 설정

- ### build.gradle

  ```gradle
  plugins {
      id 'org.springframework.boot' version '2.6.7'
      id 'io.spring.dependency-management' version '1.0.11.RELEASE'
      id 'java'
  }

  group = 'com.frank'
  version = '0.0.1-SNAPSHOT'
  sourceCompatibility = '1.8'

  repositories {
      mavenCentral()
  }

  dependencies {
      implementation 'org.springframework.boot:spring-boot-starter-web'
      implementation 'org.springframework.kafka:spring-kafka'
      testImplementation 'org.springframework.boot:spring-boot-starter-test'
      testImplementation 'org.springframework.kafka:spring-kafka-test'
  }

  tasks.named('test') {
      useJUnitPlatform()
  }
  ```

- ### application.yml
  ```yml
  spring:
  kafka:
      consumer:
      bootstrap-servers: localhost:9092
      group-id: foo
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      producer:
      bootstrap-servers: localhost:9092
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
  ```

> bean으로도 구현해보자

> https://debaeloper.tistory.com/m/25

> https://galid1.tistory.com/m/792

> https://pearlluck.tistory.com/307

> https://docs.spring.io/spring-kafka/reference/html/#getting-started

### 2.2 구현

### 1) controller, producer, consumer 구현

- ### controller

  ```java
  package com.frank.kafkatest.controller;

  import com.frank.kafkatest.service.KafkaProducer;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.web.bind.annotation.PostMapping;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RequestParam;
  import org.springframework.web.bind.annotation.RestController;

  @RestController
  @RequestMapping(value = "/kafka")
  public class KafkaController {
      private final KafkaProducer producer;

      @Autowired
      KafkaController(KafkaProducer producer) {
          this.producer = producer;
      }

      @PostMapping
      public String sendMessage(@RequestParam("message") String message) {
          this.producer.sendMessage(message);

          return "success";
      }
  }
  ```

- ### producer

  ```java
  package com.frank.kafkatest.service;

  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.kafka.core.KafkaTemplate;
  import org.springframework.stereotype.Service;

  @Service
  public class KafkaProducer {
      private static final String TOPIC = "exam";
      private final KafkaTemplate<String, String> kafkaTemplate;

      @Autowired
      public KafkaProducer(KafkaTemplate kafkaTemplate) {
          this.kafkaTemplate = kafkaTemplate;
      }

      public void sendMessage(String message) {
          System.out.println(String.format("Produce message : %s", message));
          this.kafkaTemplate.send(TOPIC, message);
      }
  }
  ```

- ### consumer

  ```java
  package com.frank.kafkatest.service;

  import org.springframework.kafka.annotation.KafkaListener;
  import org.springframework.stereotype.Service;

  import java.io.IOException;

  @Service
  public class KafkaConsumer {

      @KafkaListener(topics = "exam", groupId = "foo")
      public void consume(String message) throws IOException {
          System.out.println(String.format("Consumed message : %s", message));
      }
  }

  ```

### 2.3 테스트

> CLI로만 하는 방법 : https://oingdaddy.tistory.com/274

### 1) zookeeper 실행 : kafka 폴더 디렉토리에서 cmd로 실행

```cmd
.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties
```

### 2) broker (server) 실행 (별도의 cmd창을 띄워야 한다)

```cmd
.\bin\windows\kafka-server-start.bat .\config\server.properties
```

> 참고 : 실행하면 .properties 내부에 정의된 주소를 쓰게 되는데, 무시해도 된다. 프로젝트 설정인 application.yml에서 설정해준 주소로 오버라이드 되기 때문이다.

### 3) topic 생성

```cmd
.\bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic exam
```

- --bootstrap-server
  - --zookeeper 명령어는 depreciate 되었다고 한다 : zookeeper가 실행 중인 호스트일 필요가 없는 것 같다
  - https://stackoverflow.com/questions/46173003/bootstrap-server-vs-zookeeper-in-kafka
- localhost:9092
  - 서버 주소
- --replication-factor

  - 해당 토픽을 몇 개 복사할 건지
  - max값 : 떠있는 cluster 개수
  - 많을수록 broker 장애 발생 시 topic에 저장된 데이터의 안전성이 보장된다 vs broker 리소스를 많이 사용하게 되는 trade-off ?
    - https://kafka.apache.org/documentation/#replication
  - leader & follower
    - https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-topic-replication/

- --partitions
  - 이 토픽이 가질 partition의 개수
  - https://jyeonth.tistory.com/30

### 4) 프로젝트 실행

### 5) 메시지 pub/sub

- https://victorydntmd.tistory.com/m/348
- Postman으로 API를 요청해보자
- kafka 컨테이너의 topic 메시지를 확인해보자
  - kafka-console-producer.bat
  - kafka-console-consumer.bat
- Springboot의 로그를 확인해보자

> 추가1) auto.create.topcis.enable = true 설정을 할 경우, 프로젝트 실행 시 해당 topic이 없어도 자동 생성해서 실행된다고 한다.
>
> - server.properties에 정의해보았으나 작동하지 않는 것 같다.

> 추가2) ERROR Shutdown broker because all log dirs in c:\tmp\kafka-logs have failed
>
> - C:\tmp의 kafka-logs와 zookeeper 하위에 있는 로그파일들을 모두 제거하자. 서버 실행 시 자동 생성되므로 걱정할 필요없다.

> 추가3) Failed to rename log directory from ~
>
> - 토픽 삭제하고 서버 재실행 시 생긴 오류
> - 조치방법 : 추가2)와 같다
> - https://stackoverflow.com/questions/48114040/exception-during-topic-deletion-when-kafka-is-hosted-in-docker-in-windows
> - 기타 왠만한 오류는 로그파일 제거로 해결된다.

## offset
