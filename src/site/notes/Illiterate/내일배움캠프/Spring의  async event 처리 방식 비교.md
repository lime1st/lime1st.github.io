---
{"dg-publish":true,"permalink":"/illiterate//spring-async-event/","tags":["async"],"noteIcon":"","created":"2025-04-12T23:51:00","updated":"2025-04-18T10:39:20+09:00"}
---

# 1. SimpleApplicationEventMulticaster 설정

```java
@Configuration
public class AsyncConfig {

    @Bean(name = "applicationEventMulticaster")
    public ApplicationEventMulticaster simpleApplicationEventMulticaster() {
        SimpleApplicationEventMulticaster eventMulticaster = new SimpleApplicationEventMulticaster();
        eventMulticaster.setTaskExecutor(new SimpleAsyncTaskExecutor());
        return eventMulticaster;
    }
}
```

- 기본적으로 `SimpleAsyncTaskExecutor`는 **스레드를 무한정** 생성하며, 이는 리소스 관리에 부담을 줄 수 있다. 따라서 실제 애플리케이션에서는 `ThreadPoolTaskExecutor` 등으로 스레드 풀 크기를 제한하는 것이 좋다.

# 2. `@Async` 어노테이션을 사용

### 1. Async Configuration 설정

```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {

    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(25);
        executor.setThreadNamePrefix("AsyncExecutor-");
        executor.initialize();
        return executor;
    }
}
```

### 2. Event를 비동기로 처리하도록 Listener 수정

`Event`를 처리하는 `Listener` 클래스를 수정하여 비동기로 처리되도록 `@Async` 어노테이션을 추가

```java
import org.springframework.scheduling.annotation.Async;

@Component
@RequiredArgsConstructor
public class EventListener {

    private final JavaMailSender javaMailSender;

    @Override
    @Async
    public void handelEvent(ApplicationEvent event) {
    }
}
```

### 참고 사항

- `@EnableAsync`는 Spring의 비동기 메소드를 활성화시킨다.
- `@Async` 어노테이션은 비동기적으로 실행하고자 하는 메소드에 적용된다.

이와 같이 설정하면 이벤트를 비동기 방식으로 처리할 수 있어 메인 스레드에서 블로킹되는 현상을 피할 수 있다.

# 3. 차이점

#### 1. **단순 설정 vs 고급 설정**

- **SimpleApplicationEventMulticaster**: `SimpleAsyncTaskExecutor`는 단순하며, 직접적으로 스레드 풀 크기나 큐의 크기를 관리할 수 없으므로 스레드 관리를 세밀하게 조정해야 하는 경우에는 적합하지 않을 수 있다.
- **ThreadPoolTaskExecutor**: `ThreadPoolTaskExecutor`는 스레드 풀의 코어 크기, 최대 크기, 큐 크기 등을 설정할 수 있어 더 유연하고 세밀한 조정이 가능하다.

#### 2. **기능적 차이**

- **SimpleApplicationEventMulticaster**: 간단하게 비동기 이벤트 처리를 설정할 수 있지만, 고급 설정(예: 재사용 가능한 특정 스레드 풀 관리 등)을 요구하는 경우에는 제한적일 수 있다.
- **@Async와 ThreadPoolTaskExecutor**: 더 복잡한 설정이 가능하며, 특정 이벤트에 대해 서로 다른 스레드 풀을 사용하도록 설정할 수 있다. 이로 인해 더욱 정교한 스레드 관리와 성능 튜닝이 가능

#### 3. **성능적 차이**

- **SimpleAsyncTaskExecutor**: 새로운 스레드를 필요에 따라 생성하므로, 매우 많은 이벤트를 처리해야 하는 경우에는 성능적인 발목을 잡을 위험이 있으며, 자원 누수 문제를 발생시킬 수 있다.
- **ThreadPoolTaskExecutor**: 스레드 풀이 사전에 정의된 크기로 동작하므로, 필요 이상의 스레드를 생성하여 자원을 낭비하는 것을 방지할 수 있으며, 대량의 이벤트를 처리할 때 더 안정적인 성능을 보장할 수 있다.

---
[[Illiterate/내일배움캠프/event의 Async 처리 위치\|event의 Async 처리 위치]]