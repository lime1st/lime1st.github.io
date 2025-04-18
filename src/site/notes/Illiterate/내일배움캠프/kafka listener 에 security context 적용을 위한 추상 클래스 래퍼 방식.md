---
{"dg-publish":true,"permalink":"/illiterate//kafka-listener-security-context/","tags":["kafka","async"],"noteIcon":"","created":"2025-04-18T10:55:00","updated":"2025-04-18T10:59:48+09:00"}
---

kafka 리스너에 적용한 Security Context 추상클래스 래퍼 방식

# 1. `SecuredKafkaListener` 추상 클래스 정의

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

---

# 2. 리스너에서 상속받아 사용

```java
@Component
@RequiredArgsConstructor
public class KafkaConsumer extends SecuredKafkaListener {

	private final KafkaService service;

    @KafkaListener(topics = "topic_name")
    public void listen(String message) {
        log.info("Received message: {}", message);

        try {
            JsonNode node = objectMapper.readTree(message);
            // node 값 변환 
            // 보안 컨텍스트 세팅 후 서비스 로직 실행
            withSecurityContext(userId, () -> service.create(changenode));

        } catch (Exception e) {
            log.error("Error processing Kafka message: {}", message, e);
        }
    }
```

> `withSecurityContext(userId, () -> { ... });` 방식으로 보안 컨텍스트 관리가 추상화됨
> `clearContext()` 자동화
>  여러 Kafka 리스너에서 공통으로 재사용 가능
>  향후 `setSecurityContext()`에 로깅/트래킹 추가해도 코드 변경 최소화됨
