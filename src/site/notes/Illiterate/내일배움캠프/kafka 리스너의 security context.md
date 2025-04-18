---
{"dg-publish":true,"permalink":"/illiterate//kafka-security-context/","tags":["kafka","async"],"noteIcon":"","created":"2025-04-18T10:50:00","updated":"2025-04-18T10:54:50+09:00"}
---

비동기의 문제점에서 securitycontext를 어떻게 해야 하는지 경험하고 나서 kafka 메시지지 리스너를 구현할 때 우선 리스너에서 **수동으로 `SecurityContext`를 세팅해서** AuditAware나 Feign의 보안 헤더 인터셉터가 동작하도록 만들었다.

**더 나은 방법이나 다른 방식**이 있는지 찾아보았다.

---

# 구현 방식

현재 방식:

```java
SecurityContext context = SecurityContextHolder.createEmptyContext();
context.setAuthentication(new UsernamePasswordAuthenticationToken(userId, null, authorities));
SecurityContextHolder.setContext(context);
```

> 간단하고 명시적
> AuditAware, Feign 헤더 주입 다 정상 동작
> 매번 수동 세팅 필요 (반복됨)
> 실수로 `clearContext()`를 빼먹으면 보안 이슈 가능

# 대안 1: AOP로 공통 SecurityContext 설정 처리

모든 Kafka 리스너 앞단에 `SecurityContext`를 자동으로 세팅하는 AOP 작성

```java
@Aspect
@Component
public class KafkaSecurityContextAspect {

    @Around("@annotation(org.springframework.kafka.annotation.KafkaListener)")
    public Object injectSecurityContext(ProceedingJoinPoint joinPoint) throws Throwable {
        try {
            // 메시지 파싱 전 단계에선 userId를 모르므로, userId를 파라미터에서 추출하는 로직 필요
            // or 애초에 userId 정보를 헤더로 넘기고 MetadataContext에서 파싱하는 식도 가능
            setSecurityContext(UUID.randomUUID()); // 예시 - 실제론 메시지 파싱 결과 사용
            return joinPoint.proceed();
        } finally {
            SecurityContextHolder.clearContext();
        }
    }

    private void setSecurityContext(UUID userId) {
        SecurityContext context = SecurityContextHolder.createEmptyContext();
        List<GrantedAuthority> authorities = List.of(new SimpleGrantedAuthority("ROLE_USER"));
        context.setAuthentication(new UsernamePasswordAuthenticationToken(userId, null, authorities));
        SecurityContextHolder.setContext(context);
    }
}
```

> 단점: 메시지 바디 안의 userId를 AOP에서 직접 파악하긴 어렵기 때문에 일반적으론 사용이 제한적
> 하지만 Kafka 헤더에 userId를 따로 담는 식이면 가능.

---

# 대안 2: Kafka Listener Wrapper 사용

`@KafkaListener`를 감싸는 래퍼 컴포넌트를 만들어, 반복되는 SecurityContext 설정/해제를 자동화

```java
public abstract class SecuredKafkaListener {

    protected void withSecurityContext(UUID userId, Runnable task) {
        try {
            setSecurityContext(userId);
            task.run();
        } finally {
            SecurityContextHolder.clearContext();
        }
    }

    private void setSecurityContext(UUID userId) {
        SecurityContext context = SecurityContextHolder.createEmptyContext();
        List<GrantedAuthority> authorities = List.of(new SimpleGrantedAuthority("ROLE_USER"));
        context.setAuthentication(new UsernamePasswordAuthenticationToken(userId, null, authorities));
        SecurityContextHolder.setContext(context);
    }
}
```

리스너에서:

```java
@Component
@RequiredArgsConstructor
public class NotificationKafkaConsumer extends SecuredKafkaListener {

    private final NotificationService service;
    private final ObjectMapper objectMapper;

    @KafkaListener(topics = "notification_send")
    public void listen(String message) {
        try {
            JsonNode node = objectMapper.readTree(message);
            UUID userId = UUID.fromString(node.get("userId").asText());
            CreateNotificationCommand command = ...;

            withSecurityContext(userId, () -> service.create(command));
        } catch (JsonProcessingException e) {
            log.error(e.getMessage(), e);
        }
    }
}
```

> 반복 제거
> 실수로 `clearContext()` 안 해도 됨
> 확장 가능
  

---

# 대안 3: MessageConverter에서 userId 추출하여 context 설정

KafkaListener가 호출되기 전에 **Custom `RecordMessageConverter`**를 등록하면, 메시지를 처리하기 전에 context를 세팅할 수도 있다. 다만 이건 구조가 꽤 복잡해질 수 있어서 추천도는 낮다.

--- 
[[Illiterate/내일배움캠프/kafka listener 에 security context 적용을 위한 추상 클래스 래퍼 방식\|kafka listener 에 security context 적용을 위한 추상 클래스 래퍼 방식]]