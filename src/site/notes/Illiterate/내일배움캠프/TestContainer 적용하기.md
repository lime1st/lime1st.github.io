---
{"dg-publish":true,"permalink":"/illiterate//test-container/","tags":["test","testcontainer"],"noteIcon":"","created":"2025-02-26T23:12:00","updated":"2025-03-12T02:51:45+09:00"}
---

## TestContainer란?

`Java` 테스트 환경에서 사용되는 라이브러리로, 테스트를 위한 컨테이너를 쉽게 설정하고 관리할 수 있도록 도와줍니다. 이 라이브러리는 `Docker`를 기반으로 하여, 데이터베이스, 메시지 브로커 등 다양한 서비스를 컨테이너화하여 테스트 환경을 구성할 수 있다.

`TestContainer`를 사용하여 통합 테스트를 수행하면 실제환경과 유사한 테스트 환경을 구성할 수 있어 테스트의 신뢰성을 높일 수 있다.

## 의존성 추가

`build.gradle` 파일에 아래와 같이 테스트 컨테이너의 의존성을 먼저 추가해 준다.

```
testImplementation 'org.testcontainers:testcontainers'  
testImplementation 'org.testcontainers:junit-jupiter'

// 테스트에 사용할 컨테이너
testImplementation 'org.testcontainers:postgresql'
```

## @SpringBootTest

실제 테스트 파일에 다음과 같이 내가 사용할 컨테이너의 실행에 필요한 속성들을 설정하고 실행해 준다.

```java
private static final PostgreSQLContainer<?> container = 
// 사용할 컨테이너
new PostgreSQLContainer<>("postgres:16.3");  
  
@DynamicPropertySource  
static void configureDatabase(DynamicPropertyRegistry registry) {  
    container.start();  
    registry.add("spring.datasource.url", container::getJdbcUrl);  
    registry.add("spring.datasource.username", container::getUsername);  
    registry.add("spring.datasource.password", container::getPassword);  
}

// 테스트 종료 시 컨테이너를 중지하기
@AfterAll  
static void tearDown() {  
    container.stop();  
}
```

---
[[Illiterate/내일배움캠프/TestPropertySource 테스트 시 환경 변수 값 가져오기\|TestPropertySource 테스트 시 환경 변수 값 가져오기]]
[[Illiterate/내일배움캠프/MockMvc, MockMvcTester 비교\|MockMvc, MockMvcTester 비교]]