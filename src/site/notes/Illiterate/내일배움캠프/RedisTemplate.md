---
{"dg-publish":true,"permalink":"/illiterate//redis-template/","tags":["redis","redistemplate"],"noteIcon":"","created":"2025-03-05T10:36:00","updated":"2025-03-05T10:36:33+09:00"}
---

`SpringBoot`에서 `Redis`를 사용할 경우 다른 DB와 같이 `spring-data-jpa`서 제공하는 Repository를 사용해 CRUD 작업을 손쉽게 만들 수 있다.

다만 Redia의 자료형을 활용한 기능을 만드는 데는 한계가 있다. `Redis`의 `Key`를 직접 설정하고 자료형도 직접 선택해 가면서 활용하고 싶다면 `RedisTemplate`을 사용하면 된다. `RedisTemplate`을 정의하면서 Key와 Value로 사용될 Java 클래스를 정하고, 이후 사용할 세부 작업을 `RedisOperations`를 이용해 가져올 수 있다.

## StringRedisTemplate

복잡한 작업 없이 Java의 문자열만 드루는 경우, Key와 Value를 전부 `Java의 문자열(String Type)`이라고 가정, 문자열 데이터를 주고받기 위한 작업들을 준비하며, 기본 설정을 가지고 자동으로 만들어져 주입되는 `Spring Bean`이다.


> [!NOTE] 주의!
> Redis의 String 자료형만 사용하는 것이 아닌, Redis와 Spring Boot 사이에 데이터를 주고받는 과정에서 직렬화 - 역직렬화 할 때 Java의 String으로 취급되는 클래스!
> Redis List에 넣을 때 Java 문자열을 넣고, Redist Set에 넣을 때 Java 문자열을 넣는다는 의미!


```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, ItemDto> itemRedisTemplate(
            RedisConnectionFactory connectionFactory
    ) {
        RedisTemplate<String, ItemDto> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        template.setKeySerializer(RedisSerializer.string());
        template.setValueSerializer(RedisSerializer.json());
        return template; 
    }
}
```
---
[[Illiterate/내일배움캠프/Redis\|Redis]]