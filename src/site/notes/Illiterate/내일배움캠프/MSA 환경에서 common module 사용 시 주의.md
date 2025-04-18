---
{"dg-publish":true,"permalink":"/illiterate//msa-common-module/","tags":["msa","common","module"],"noteIcon":"","created":"2025-04-18T10:21:00","updated":"2025-04-18T10:21:21+09:00"}
---

스프링부트 버전이 올라오면서 자주 사용되는 것들이 생략되는 것이 많아져 신경을 안쓰고 있었는데 common 모듈에 정의되어 있는 @Bean 들이 사용하는 모듈에 자동으로 등록되지 않는 경우가 있어서 찾아보았다.

결론부터 말하면:

> **자동으로 등록되지 않는다.**

---

멀티 모듈(MSA 또는 Multi-Module Project) 구조에서의 `@Bean`, `@Component`, `@RestControllerAdvice` 동작 정리

# 1. **common 모듈에 정의된 클래스들이 자동으로 등록되지 않는 이유**

- Spring은 **component scan**에 따라 빈을 등록하는데 `common` 모듈의 클래스를 `@Component`, `@RestControllerAdvice`, `@Configuration` 등으로 정의했더라도 **현재 실행되는 앱**의 `@SpringBootApplication` 클래스에서 **scan 범위에 포함되지 않으면** 등록되지 않음.

### 예시

```java
@SpringBootApplication(scanBasePackages = "com.ex.common")
// 또는 scanBasePackages = {"com.ex.this", "com.ex.common"}
public class ThisApplication { ... }
```

`@SpringBootApplication` 안에 `scanBasePackages`가 명시돼 있지 않으면,  
**기본적으로 자기 패키지 하위만 스캔**
즉 `com.ex.this` 아래에 있다면 `com.ex.common`은 스캔되지 않음

---

# 2. 해결 방법

### 방법 ①: `@SpringBootApplication(scanBasePackages = "...")` 설정

```java
@SpringBootApplication(scanBasePackages = "com.ex")
```

→ `common`, `notification`, `member` 등 모든 모듈이 포함되도록 공통 prefix로 스캔

---

### 방법 ②: 필요한 클래스를 `@Import`로 수동 등록

```java
@Import(GlobalExceptionHandler.class)
@SpringBootApplication
public class NotificationApplication { ... }
```

→ `@RestControllerAdvice`나 `@Configuration`이 명확하게 빈 등록되도록 `@Import`로 수동 등록도 가능.

---

# 결론

- `common` 모듈의 `@RestControllerAdvice`나 `@Bean`들은 **자동 등록되지 않음**
    
- 명시적으로:
    
    - `@SpringBootApplication(scanBasePackages = "...")` 설정하거나,
        
    - `@Import(...)` 또는 `@ComponentScan(...)`으로 등록해야 함
        

---