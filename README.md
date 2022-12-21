# 강의 정리

## [Service Discovery] User Service - 등록

### 서비스 실행 시 포트 바꾸는 방법

#### `application.yml` 변경

```yml
server:
  port: 9002
```

* 단, 서로 다른 포트의 서비스를 띄울 수 없음

### 2. Intellij 기능 이용

<img width="607" alt="image" src="https://user-images.githubusercontent.com/28076542/208416714-d4ae8899-1d19-4e89-a06b-120d57298262.png">

* Edit Configurations -> UserServiceApplication-2 생성 -> VM Option: `-Dserver.port=9002`

### 3. gradle bootRun

```bash
./gradlew clean bootRun --args='--server.port=9003'
```

### 4. gradle build 후 java -jar

```bash
./gradlew clean build
cd build/libs
java -jar .\user-service-0.0.1-SNAPSHOT.jar --server.port=9004
```

---

## [Service Discovery] User Service - Load Balancer

### `application.yml`에서 랜덤 포트 설정

```yml
server:
  port: 0
```

### 설정 후 다음 명령어로 서로 다른 포트를 가진 서비스 실행 가능

```bash
./gradlew bootRun --args='--server.port=9003'
```

* 하지만 Eureka에는 인스턴스가 하나만 표시됨

### `application.yml` 설정으로 서로 다른 이름의 인스턴스가 나오도록 함

```yml
eureka:
  instance:
    instance-id: ${spring.cloud.client.hostname}:${spring.application.instance_id:${random.valume}}
```

---

## [API Gateway Service] Spring Cloud Gateway - 프로젝트 생성

### Controller의 작성 시 주의 사항

#### 순수 서비스를 띄울 시

```java
@RequestMapping("/")
public class FirstServiceController {
    ...
}
```

* url: `http://localhost:8081/welcome`

#### API Gateway 통해서 들어올 시

```java
@RequestMapping("/first-service")
public class FirstServiceController {
    ...
}
```

* url: `http://localhost:8000/first-service/welcome`

---

## [API Gateway Service] Spring Cloud Gateway - Logging Filter

### 게이트웨이부터 서비스까지 필터가 적용되는 순서

#### Logging Filter의 Order가 `Ordered.LOWEST_PRECEDENCE`일 경우

<img width="1201" alt="image" src="https://user-images.githubusercontent.com/28076542/208709812-881e46dc-cbb8-406e-b03b-0fdcc54f2123.png">

* Order가 `Ordered.HIGHEST_PRECEDENCE`인 경우엔 Logging Filter가 가장 먼저, 가장 나중에 호출됨

---

## [API Gateway Service] Spring Cloud Gateway - Load Balancer 1

<img width="860" alt="image" src="https://user-images.githubusercontent.com/28076542/208713924-773b82f5-e623-4069-949e-a4a3a9439254.png">

---

## [E-commerce 애플리케이션] - E-commerce 애플리케이션 개요

<img width="1177" alt="image" src="https://user-images.githubusercontent.com/28076542/208722585-4d302a15-5e33-42b8-9adf-3664b74e2287.png">

---

## [E-commerce 애플리케이션] - E-commerce 애플리케이션 구성

### 강의 애플리케이션 구성

<img width="886" alt="image" src="https://user-images.githubusercontent.com/28076542/208725159-b145e144-ceb4-4d4c-9d7a-98b26e9b1a19.png">

### 심화된 애플리케이션 구성

<img width="857" alt="image" src="https://user-images.githubusercontent.com/28076542/208725328-b1924c2e-4012-4803-be7a-4b00593150d1.png">

### 애플리케이션 구성요소

<img width="811" alt="image" src="https://user-images.githubusercontent.com/28076542/208725569-515db711-adc4-4310-af36-29bf1dc9df49.png">

### 애플리케이션 APIs

<img width="841" alt="image" src="https://user-images.githubusercontent.com/28076542/208725701-4f613148-d70e-44fc-8e98-800299ccdba8.png">

---

## [Users Microservice 1] - welcome() 메소드

* discoveryservice(Eureka)는 Intellij가 아닌 `./gradlew clean bootRun`으로 실행\

---

## [Users Microservice 1] - H2 데이터베이스 연동

### build.gradle

```gradle
dependencies {
  ...
	testImplementation group: 'com.h2database', name: 'h2', version: '2.1.214'
	implementation group: 'com.h2database', name: 'h2', version: '2.1.214'
}
```

### H2 Windows Installer 설치

* http://www.h2database.com/html/download.html

### 데이터베이스 생성

<img width="118" alt="image" src="https://user-images.githubusercontent.com/28076542/208739707-9989d988-19fe-46ab-8f10-4f6c2799c617.png">

<img width="429" alt="image" src="https://user-images.githubusercontent.com/28076542/208739966-707ea093-22e6-4372-87e2-5556f997d415.png">

<img width="342" alt="image" src="https://user-images.githubusercontent.com/28076542/208740022-e37864b0-e9e4-470f-95b0-304682c4f76f.png">

---

## [Users Microservice 1] - 사용자 추가

<img width="509" alt="image" src="https://user-images.githubusercontent.com/28076542/208741086-ada00bba-b7f7-4786-8f33-fca1dca3e718.png">

* dto 하나로 해도 상관 없음

---

## [Users Microservice 1] - JPA1

### spring boot 2.3 버전 이상부턴 validator 추가해야 함

```gradle
dependencies {
  ...
	implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

---

## [Users Microservice 1] - JPA2

### `application.yml`에 이거 추가해야 테이블 자동 생성됨

```yml
  jpa:
    hibernate:
      ddl-auto: create
```

### `ResponseEntity` 반환 방법

```java
return new ResponseEntity(responseUser, HttpStatus.CREATED);
```

