---
{"dg-publish":true,"permalink":"/illiterate//event-async/","tags":["event","async"],"noteIcon":"","created":"2025-04-18T10:36:00","updated":"2025-04-19T04:17:22+09:00"}
---

# 1. @Async 어노테이션을 이벤트 발행 메서드에 적용

이벤트 발행 자체를 비동기적으로 수행하려면 `@Async` 어노테이션을 이벤트 발행 메서드에 적용할 수 있다. 이렇게 하면 이벤트 발생 이후의 처리가 비동기적으로 이루어진다.

```java
@Service
public class EmailEventPublisher implements ApplicationEventPublisher {

    private final ApplicationEventPublisher publisher;

    public EmailEventPublisher(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    @Async
    @Override
    public void publish() {
        publisher.publishEvent(new EmailSendEvent(this, context));
    }
}
```

# 2. @Async 어노테이션을 이벤트 리스너에 적용

이벤트 발행은 즉시 수행하되, 이벤트 리스너가 이벤트를 비동기적으로 처리한다. 이 경우 이벤트 처리 시간이 애플리케이션의 응답 시간에 영향을 미치지 않는다.

```java
@Component
public class EmailEventListener {

    @Async
    @EventListener
    public void handleEmailSendEvent(EmailSendEvent event) {
        // 이메일 전송 로직
        EmailContext context = event.getContext();
        // 예: EmailService.sendEmail(context);
    }
}
```

# 비교

### 이벤트 발행 메서드에 @Async 적용

- **장점**: 이벤트가 즉시 비동기적으로 발행되며, 발행 로직이 비즈니스 서비스 로직으로부터 분리.
- **단점**: 비동기적으로 발행된 이벤트가 여러 개 있을 경우, 이벤트 간의 종속성이 있으면 문제가 발생할 여지가 있다.

### 이벤트 리스너에 @Async 적용

- **장점**: 이벤트 발행은 동기적으로 이루어져 **로직의 순서가 보장**되고, 이벤트 처리만 비동기적으로 수행
- **단점**: 발행된 이벤트가 즉시 처리되지 않아도 되는 경우에는 비효율적


일반적으로 **이벤트 리스너**에 `@Async`를 적용하는 것이 더 유연하고, 이벤트 발행과 처리 간의 명확한 책임 분리를 제공한다. 이러한 방식으로 구현하면 이벤트 처리 시간이 애플리케이션의 응답 시간에 영향을 미치지 않으며, 다양한 비즈니스 로직을 독립적으로 확장할 수 있다.

---
[[Illiterate/내일배움캠프/비동기(@Async) 처리시 주의\|비동기(@Async) 처리시 주의]]