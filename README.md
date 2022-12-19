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
./gradlew clean bootRun
./gradlew bootRun --args='--server.port=9003'
```

### 4. gradle build 후 java -jar

```bash
./gradlew clean bootRun
./gradlew build
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