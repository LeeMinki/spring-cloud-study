# 강의 정리

## [Service Discovery] User Service - 등록

### 서비스 실행 시 포트 바꾸는 방법

### 1. `application.yml` 변경

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

## [E-commerce 애플리케이션] E-commerce 애플리케이션 개요

<img width="1177" alt="image" src="https://user-images.githubusercontent.com/28076542/208722585-4d302a15-5e33-42b8-9adf-3664b74e2287.png">

---

## [E-commerce 애플리케이션] E-commerce 애플리케이션 구성

### 강의 애플리케이션 구성

<img width="886" alt="image" src="https://user-images.githubusercontent.com/28076542/208725159-b145e144-ceb4-4d4c-9d7a-98b26e9b1a19.png">

### 심화된 애플리케이션 구성

<img width="857" alt="image" src="https://user-images.githubusercontent.com/28076542/208725328-b1924c2e-4012-4803-be7a-4b00593150d1.png">

### 애플리케이션 구성요소

<img width="811" alt="image" src="https://user-images.githubusercontent.com/28076542/208725569-515db711-adc4-4310-af36-29bf1dc9df49.png">

### 애플리케이션 APIs

<img width="841" alt="image" src="https://user-images.githubusercontent.com/28076542/208725701-4f613148-d70e-44fc-8e98-800299ccdba8.png">

---

## [Users Microservice 1] welcome() 메소드

* discoveryservice(Eureka)는 Intellij가 아닌 `./gradlew clean bootRun`으로 실행

---

## [Users Microservice 1] H2 데이터베이스 연동

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

## [Users Microservice 1] 사용자 추가

<img width="509" alt="image" src="https://user-images.githubusercontent.com/28076542/208741086-ada00bba-b7f7-4786-8f33-fca1dca3e718.png">

* dto 하나로 해도 상관 없음

---

## [Users Microservice 1] JPA1

### spring boot 2.3 버전 이상부턴 validator 추가해야 함

```gradle
dependencies {
  ...
	implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

---

## [Users Microservice 1] JPA2

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

---

## [Users Microservice 1] Spring Security 연동

### Spring Security configure에서

* `http.headers().frameOptions().disable()`을 추가하지 않으면 h2-console 접근 안 됨

### Spring Security deprecated

#### `WebSecurityConfigurerAdapter` deprecated

* [공식 문서](https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter)
* Spring Boot 2.7.x부터 deprecated
* `SecurityFilterChain`을 Bean으로 등록해서 사용해야 함
* `@EnableWebSecurity`은 그래도 붙여야 하는 듯

#### `authorizeRequests`, `antMatchers` deprecated

* [참고](https://stackoverflow.com/questions/74609057/how-to-fix-spring-authorizerequests-is-deprecated)
* `authorizeRequests`, `antMatchers` -> `authorizeHttpRequests`, `requestMatchers`

#### `requestMatchers`에 h2-console을 추가해도 안 됨

* [이걸로 해결](https://stackoverflow.com/questions/74680244/h2-database-console-not-opening-with-spring-security)

#### 결론은 이렇게 작성

```java
import static org.springframework.boot.autoconfigure.security.servlet.PathRequest.toH2Console;
@Configuration
@EnableWebSecurity
public class WebSecurity {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf(csrf->csrf.disable());
        http
                .authorizeHttpRequests(authz -> authz
                        .requestMatchers("/users/**").permitAll()
                        .requestMatchers(toH2Console()).permitAll()
                );
        http.headers().frameOptions().disable();
        return http.build();
    }
}
```

### Spring Security 적용 후 프로젝트 시작 시 로그에서 나오는 password

#### `Using generated security password: 2d6f80a0-9169-4149-a047-ae0f05d51da9`

* Spring Boot Project 내 인증에서 사용될 password임

### BCryptPasswordEncoder

* Bean으로 `UserServiceApplication`에 등록
* salt로 같은 패스워드여도 다른 해시값이 나옴

---

## [Catalogs and Orders Microservice] Users Microservice와 Spring Cloud Gateway 연동

### API Gateway URI와 서비스 URI가 달라서 생기는 문제

<img width="874" alt="image" src="https://user-images.githubusercontent.com/28076542/209029990-1175f4c0-e677-44f5-b36e-7dbf171ba09b.png">

* 서비스에서 `/user-service/health_check`이 없어서 NOT FOUND가 뜸

### 해결 방법

```java
    @GetMapping("/user-service/health_check")
    public String status() {
        return String.format("It's Working in User Service on PORT %s", env.getProperty("local.server.port"));
    }
```

---

## [Catalogs and Orders Microservice] Catalogs Microservice - 기능구현 2

### 실행 시 `data.sql` 실행시키기 위한 `application.yml` 설정

```yml
  h2:
    console:
      enabled: true
      settings:
        web-allow-others: true
      path: /h2-console
  jpa:
    hibernate:
      ddl-auto: create-drop
    show-sql: true
    generate-ddl: true
    database: h2
    defer-datasource-initialization: true
  sql:
    init:
      mode: always
      schema-location: classpath:data.sql
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:~/test
    username: sa
    password: abc
```

---

## [Catalogs and Orders Microservice] Orders Microservice - 기능 구현 2

### 여러 프로세스에서 H2 DB를 띄우기 위한 설정

#### `application.yml` -> `datasource.url` -> `AUTO_SERVER=true`

```yml
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:~/test;AUTO_SERVER=true
    username: sa
    password: abc
```

---

## [Users Microservice 2] Users Microservice - AuthenticationFilter 추가

### `hasIpAddress` deprecated 대안

* [참고](https://stackoverflow.com/questions/72366267/matching-ip-address-with-authorizehttprequests)

### `AuthenticationManager` 가져오는 방법

* `WebSecurityConfigurerAdapter`가 없으니 난리났음
* `AuthenticationManager`는 bean으로 등록하고 사용해야 함
* `AuthenticationManager` 주입 방법에서 많이 해맴
* [AuthenticationManager 주입 참고](https://stackoverflow.com/questions/72381114/spring-security-upgrading-the-deprecated-websecurityconfigureradapter-in-spring)
* [결정타](https://stackoverflow.com/questions/73508743/implementing-new-customfilter-in-spring-security)

### 최종 코드

#### WebSecurity.java

```java
@Configuration
@EnableWebSecurity
public class WebSecurity {
    private final String IP_ADDRESS = "218.147.172.23";

    private AuthenticationConfiguration authenticationConfiguration;
    private Environment env;
    private UserService userService;
    private BCryptPasswordEncoder bCryptPasswordEncoder;

    @Autowired
    public WebSecurity(AuthenticationConfiguration authenticationConfiguration,
                       Environment env,
                       UserService userService,
                       BCryptPasswordEncoder bCryptPasswordEncoder) {
        this.authenticationConfiguration = authenticationConfiguration;
        this.env = env;
        this.userService = userService;
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
    }

    @Bean
    public AuthenticationManager authenticationManager() throws Exception {
        return this.authenticationConfiguration.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        AuthenticationManager authenticationManager = authenticationManager();
        http.csrf(csrf -> csrf.disable());
        http
                .authorizeHttpRequests(authz -> authz
//                        .requestMatchers("/user-service/users/**", "/user-service/health_check/**", "/user-service/welcome/**").permitAll()
                                .requestMatchers(toH2Console()).permitAll()
                                .requestMatchers("/**")
                                .access(hasIpAddress(IP_ADDRESS))
                                .and()
                                .addFilter(new AuthenticationFilter(authenticationManager))
                );
        http.headers().frameOptions().disable();
        return http.build();
    }

    private static AuthorizationManager<RequestAuthorizationContext> hasIpAddress(String ipAddress) {
        IpAddressMatcher ipAddressMatcher = new IpAddressMatcher(ipAddress);
        return (authentication, context) -> {
            HttpServletRequest request = context.getRequest();
            return new AuthorizationDecision(ipAddressMatcher.matches(request));
        };
    }
}
```

#### AuthenticationFilter.java

```java
public class AuthenticationFilter extends UsernamePasswordAuthenticationFilter {

    public AuthenticationFilter(AuthenticationManager authenticationManager) {
        super.setAuthenticationManager(authenticationManager);
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request,
                                                HttpServletResponse response) throws AuthenticationException {
        try {
            RequestLogin creds = new ObjectMapper().readValue(request.getInputStream(), RequestLogin.class);

            return getAuthenticationManager().authenticate(
                    new UsernamePasswordAuthenticationToken(
                            creds.getEmail(),
                            creds.getPassword(),
                            new ArrayList<>()));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    protected void successfulAuthentication(HttpServletRequest request,
                                            HttpServletResponse response,
                                            FilterChain chain,
                                            Authentication authResult) throws IOException, ServletException {
//        super.successfulAuthentication(request, response, chain, authResult);
    }
}
```

---

## [Users Microservice 2] Users Microservice - 로그인 처리 과정

![image](https://user-images.githubusercontent.com/28076542/209203316-64b805c8-6e5b-4338-be68-f7076dba8246.png)

---

## [Users Microservice 2] Users Microservice - JWT 생성

### 아래 코드에서 Exception  발생

`java.lang.ClassNotFoundException: javax.xml.bind.DatatypeConverter`

```java
String token = Jwts.builder()
        .setSubject(userDetails.getUserId())
        .setExpiration(new Date(System.currentTimeMillis() +
                Long.parseLong(env.getProperty("token.expiration_time"))))
        .signWith(SignatureAlgorithm.HS512, env.getProperty("token.secret"))
        .compact();
```

#### `jjwt` 외에 `jaxb-api` dependency도 추가해야 함

```gradle
dependencies {
  ...
	// https://mvnrepository.com/artifact/io.jsonwebtoken/jjwt
	implementation 'io.jsonwebtoken:jjwt:0.9.1'
	// https://mvnrepository.com/artifact/javax.xml.bind/jaxb-api
	implementation 'javax.xml.bind:jaxb-api:2.3.1'
}
```

---

## [Configuration Service] Spring Cloud Config

<img width="520" alt="image" src="https://user-images.githubusercontent.com/28076542/209279101-67c62f2b-5454-49bd-8a0e-7707746f2a97.png">

---

## [Configuration Service] Local Git Repository

### 우선순위

<img width="839" alt="image" src="https://user-images.githubusercontent.com/28076542/209281756-ff577fe2-7a3d-4755-b541-a5b38d3310f2.png">

* 외부의 설정 파일이 우선순위가 더 높음
* `application-name-{profile}.yml`과 같이 구체적인 파일명이 우선순위가 더 높음
* profile을 명시하지 않으면 `default`

---

## [Configuration Service] Spring Cloud Config - 프로젝트 생성

### `ecommerce.yml`

```yml
token:
  expiration_time: 86400000 # 하루
  secret: user_token_test

gateway:
  ip: 218.147.172.23

```

* 현재 디렉토리의 바깥에 있는 cloud-config 디렉토리에 `ecommerce.yml` 생성
* cloud-config 디렉토리에서 `git init`

### Windows에서 git uri 설정

```yml
spring:
  application:
    name: config-service
  cloud:
    config:
      server:
        git:
          uri: file:///C:\Users\kai01\Desktop\cloud-config

```

---

## [Configuration Service] Users Microservice에서 Spring Cloud Config 연동 2

### Configuration value 변경 반영 방법

#### 1. 서버 재기동

* 좋은 방법이 아님

#### 2. Actuator refresh

* Actuator refresh로 새로운 정보를 가져올 수 있음

##### Spring Boot Actuator

* Application 상태 모니터링
* Metric 수집을 위한 Http End point 제공
* Application마다 Refresh를 각각 실행시켜야 함

##### configuration 변경 후 아래와 같이 POST로 refersh 전송하면, 변경내용 나옴

<img width="636" alt="image" src="https://user-images.githubusercontent.com/28076542/209300441-e2988d3d-8f6a-427c-b070-bb3c8ac4471c.png">

#### 3. Spring cloud bus 사용

* Actuator보다 더 효율적임

---

## [Configuration Service] Spring Cloud Gateway에서 Spring Cloud Config 연동 2

### `HttpTrace` -> `HttpExchanges`로 이름 변경

* [issue 참고](https://github.com/spring-projects/spring-boot/issues/32885)


### `ApigatewayServiceApplication`에서 `@Bean` 등록

```java
	@Bean
	public HttpExchangeRepository httpExchangeRepository() {
		return new InMemoryHttpExchangeRepository();
	}
```

---

## [Spring Cloud Bus] Spring Cloud Bus 개요

<img width="798" alt="image" src="https://user-images.githubusercontent.com/28076542/209323792-8555071e-b57e-4128-981c-43d123287986.png">

<img width="870" alt="image" src="https://user-images.githubusercontent.com/28076542/209396001-68447641-ef72-41de-8dc1-1dfcebdfb910.png">

### busrefresh

<img width="803" alt="image" src="https://user-images.githubusercontent.com/28076542/209396354-b3a12207-bfe1-4aa8-ae85-12c6c61afa4c.png">

* busrefresh를 어느 서비스에 호출하더라도, Spring Cloud Bus에 연결된 모든 서비스에 호출됨

---

## [Spring Cloud Bus] AMQP 사용

* Advanced Message Queing Protocol

### Configuration의 rabbitmq

* configuration에 변경 요청이 오면 rabbitmq에 변경사항을 통보
* rabbitmq에 등록된 다른 microservice에 push

### rabbitmq 접속 로그

```bash
INFO 38044 --- [           main] o.s.a.r.c.CachingConnectionFactory       : Attempting to connect to: [127.0.0.1:5672]
INFO 38044 --- [           main] o.s.a.r.c.CachingConnectionFactory       : Created new connection: rabbitConnectionFactory#4f7bb8df:0/SimpleConnection@719bb60d [delegate=amqp://guest@127.0.0.1:5672/, localPort=9345]
INFO 38044 --- [           main] o.s.c.stream.binder.BinderErrorChannel   : Channel 'rabbit-283438643.springCloudBusInput.errors' has 1 subscriber(s).
```

---

## [Spring Cloud Bus] Spring Cloud Bus 테스트

### 설정 정보 업데이트

<img width="645" alt="image" src="https://user-images.githubusercontent.com/28076542/209402055-84920a47-a5b8-4a74-8bd9-f319fce6b7ec.png">


### 전송 후 로그

#### user-service

```bash
INFO 3300 --- [nfoReplicator-0] com.netflix.discovery.DiscoveryClient    : DiscoveryClient_USER-SERVICE/user-service:a8eba4416a7f58528aa41e606be7b2aa - registration status: 204
```

#### apigateway-service는 Exception

`Reason: java.lang.NoSuchMethodException: java.security.Provider.<init>()`

* [버전 이슈인듯](https://github.com/spring-cloud/spring-cloud-gateway/issues/2816)

---

## [설정 정보의 암호화 처리] 대칭키와 비대칭키

<img width="745" alt="image" src="https://user-images.githubusercontent.com/28076542/210038413-39262eba-fa76-437d-9d9a-f628ae1b9296.png">

---

## [설정 정보의 암호화 처리] 대칭키를 이용한 암호화 2

### 여러 서비스에서 h2-console에 접속하기 위해 jdbc url 변경

<img width="452" alt="image" src="https://user-images.githubusercontent.com/28076542/210041264-7bf17ae0-2810-4f24-9b50-c3b6a792ac72.png">

```yml
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:tcp://localhost/~/test;AUTO_SERVER=true
    username: sa
    password: abc
```

* `jdbc:h2:~/test;AUTO_SERVER=true` -> `jdbc:h2:tcp://localhost/~/test;AUTO_SERVER=true`