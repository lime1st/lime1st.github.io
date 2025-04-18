---
{"dg-publish":true,"permalink":"/illiterate//acync/","tags":["async"],"noteIcon":"","created":"2025-04-18T10:50:00","updated":"2025-04-18T10:50:41+09:00"}
---

두 번째 프로젝트의 피드백에서 ThreadLocal을 사용하는 경우 비동기 처리에 주의하라고 하셨던 것을 피드백에 대한 글도 작성해 놓고도 생각을 못해서 한참 헤메게 되었다..ㅠㅠ

이벤트를 @Acync 어노테이션을 활용해 비동기로 동작하게 만들면서 이벤트 리스너에서 FeignClient를 호출하고 있었는데 이전에는 잘 동작하던 api가 계속 403 에러가 났었다.
문제는 리스너가 비동기로 작동하게 되면서 Thread를 새로 만들어서 작업하게 되는데 이 때 SecurityContext를 넘겨주지 않아서 권한 문제가 계속 발생했던 것이다..

# 해결 방법

### 1. 권한 정보를 비동기 처리 전에 전달해서 사용

**비동기 이벤트 처리 전에 헤더 정보를 꺼내서 DTO로 같이 넘기기.**

```java
// 이벤트 발생 전
UUID userId = (UUID) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
eventPublisher.publishEvent(new NotificationEvent(userId, ...));
```

→ 그리고 `EventHandler`에서는 받은 `userId`로 Feign 호출.

---

### 2. Feign 호출에 SecurityContext 없이도 사용할 수 있도록 예외 처리

```java
if (authentication != null && authentication.isAuthenticated() && authentication.getPrincipal() instanceof UUID userId) {
    requestTemplate.header("X-User-Id", userId.toString());
} else {
    log.warn("Skipping Feign header injection: No authentication or unexpected principal type");
}
```

---

### 3. `SecurityContext`를 `@Async`에서 복사하려면?

```java
@EnableAsync
@Configuration
public class AsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        return new DelegatingSecurityContextExecutor(Executors.newCachedThreadPool());
    }
}
```

> 이렇게 하면 `@Async` 메서드에서도 `SecurityContext` 유지된다. 단, `@Async`만 해당되고 Kafka나 다른 비동기 처리에는 다른 설정을...

---
### 내가 처리한 방법

```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor delegate = new ThreadPoolTaskExecutor();
        delegate.setCorePoolSize(5);
        delegate.setMaxPoolSize(10);
        delegate.setQueueCapacity(25);
        delegate.setThreadNamePrefix("AsyncExecutor-");
        delegate.initialize();

        // DelegatingSecurityContextAsyncTaskExecutor로 감싸기!
        return new DelegatingSecurityContextAsyncTaskExecutor(delegate);
    }
}

```

이렇게 하면 `@Async`로 실행된 메서드 안에서도 `SecurityContextHolder.getContext()`로 유저 정보 접근이 가능해진다. Feign 인터셉터에서도 `X-User-Id` 헤더 정상 주입됨.


---
[[Illiterate/내일배움캠프/kafka 리스너의 security context\|kafka 리스너의 security context]]