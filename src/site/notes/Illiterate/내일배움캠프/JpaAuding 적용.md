---
{"dg-publish":true,"permalink":"/illiterate//jpa-auding/","tags":["jpa","auditing"],"noteIcon":"","created":"2025-03-25T18:11:00","updated":"2025-03-25T18:11:45+09:00"}
---

## 수정 전

msa 환경에서 인증/인가를 gateway에서 처리하므로 각 서비스에서는 spring-security 의존성을 제거하고 헤더에 사용자 정보를 사용 중

```java
@Configuration  
@EnableJpaAuditing  
@RequiredArgsConstructor  
public class JpaAuditorAware implements AuditorAware<String> {  
  
    private final HttpServletRequest request;  
  
    @Override  
    public Optional<String> getCurrentAuditor() {  
        log.info("getCurrentAuditor");  
        log.info("X-Username: {}", request.getHeader("X-Username"));  
        return Optional.ofNullable(request.getHeader("X-Username"));  
    }  
}
```

현재 `JpaAuditorAware` 클래스에서 `@EnableJpaAuditing`을 선언했지만, `AuditorAware`가 정상적으로 동작하지 않는 이유는 `HttpServletRequest`를 직접 주입받고 있기 때문일 가능성이 높고 spring이 `AuditorAware`를 인스턴스화할 때, `HttpServletRequest`가 아직 존재하지 않거나 빈으로 주입되지 않을 수도 있음.

## 해결 방법

1. `HttpServletRequest`를 직접 주입하지 않고, `RequestContextHolder`를 사용해 가져오기.
2. `@Component`를 사용하여 `AuditorAware`를 별도 빈으로 등록.

---
## 수정 코드

```java
@Slf4j(topic = "JpaAuditor")
@Component
public class JpaAuditorAware implements AuditorAware<String> {

    @Override
    public Optional<String> getCurrentAuditor() {
        ServletRequestAttributes attributes = 
            (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();

        if (attributes == null) {
            log.info("RequestAttributes is null");
            return Optional.empty();
        }

        HttpServletRequest request = attributes.getRequest();
        String username = request.getHeader("X-Username");

        log.info("getCurrentAuditor");
        log.info("X-Username: {}", username);

        return Optional.ofNullable(username);
    }
}
```

- `RequestContextHolder`를 사용하여 현재 요청을 가져옴.
- 요청이 없는 상황에서는 `Optional.empty()`를 반환하여 예외 방지.

## @EnableJpaAuditing을 별도 설정 클래스로 이동

현재 `JpaAuditorAware` 클래스에 `@EnableJpaAuditing`이 선언되어 있는데,  
이 옵션은 일반적으로 `@Configuration` 클래스에서 별도로 선언하는 것이 좋음.

```java
@Configuration
@EnableJpaAuditing(auditorAwareRef = "jpaAuditorAware")
public class JpaConfig {
}
```

- `auditorAwareRef = "jpaAuditorAware"`로 인해 Spring이 올바르게 빈을 찾도록 보장됨.
- `JpaAuditorAware`는 **별도의 `@Component` 빈으로 등록**되고 JPA 설정과 분리됨.

---

**Spring Boot 3.2부터는 `@EntityListeners(AuditingEntityListener.class)`가 생략 가능**하지만, `AuditorAware`을 사용할 경우에는 여전히 명시적으로 추가해야 한다.

- Spring Boot 3.2부터 **JPA Auditing 기능(`@CreatedDate`, `@LastModifiedDate`)은 자동으로 활성화**됨.
- 하지만, **`@CreatedBy`, `@LastModifiedBy` 같은 Auditor 관련 기능은 `AuditorAware`이 필요**하고,  이 경우 **`@EntityListeners(AuditingEntityListener.class)`를 생략하면 동작하지 않음**

즉, 날짜 기반 자동 감사(`@CreatedDate`, `@LastModifiedDate`)만 사용할 경우 생략 가능하지만,  
사용자 정보를 추적하는 `AuditorAware`를 함께 사용하면 반드시`@EntityListeners(AuditingEntityListener.class)`를 추가해야 함.