---
{"dg-publish":true,"permalink":"/illiterate//naming-convention/","tags":["convention","staticFactoryMethod"],"noteIcon":"","created":"2025-02-16T15:49:00","updated":"2025-04-01T15:52:41+09:00"}
---

프로젝트 팀원 분이 공유해 주신 내용에서 내가 당장 외우고 있으면 좋을 내용만 정리해보았다.

# 네이밍 종류

### from:

하나의 매개 변수를 받아서 객체를 생성한다.
- **다른 타입의 객체를 변환할 때 사용**
- 주로 **DTO → 도메인 객체 변환**에 많이 쓰임
- **대표적인 예시:** `Instant.from(TemporalAccessor)`

```java
public static User from(UserDto dto) {
    return new User(dto.getName(), dto.getEmail());
}

User user = User.from(userDto);
```
### of:

여러 개의 매개 변수를 받아서 객체를 생성한다.
- 단순한 정적 팩토리 메서드로, **입력값을 기반으로 객체를 생성**할 때 사용.
- 생성자보다 **가독성이 좋고, 의미가 명확함.**
- **대표적인 예시:** `EnumSet.of()`, `LocalDate.of()`

```java
public static User of(String name, String email) {
    return new User(name, email);
}

User user = User.of("John Doe", "john@example.com");
```

### valueOf:

기존 객체를 참조하거나 변환할 때 사용
- `of()`와 비슷하지만, **기존 인스턴스를 반환할 가능성이 높음**
- 보통 `String → Enum` 변환에 많이 쓰임
- **대표적인 예시:** `Integer.valueOf()`, `Enum.valueOf()`

```java
public static Role valueOf(String roleName) {
    return switch (roleName.toUpperCase()) {
        case "ADMIN" -> Role.ADMIN;
        case "USER" -> Role.USER;
        default -> throw new IllegalArgumentException("Unknown role: " + roleName);
    };
}

Role admin = Role.valueOf("ADMIN");
```
### getInstance | instance:

인스턴스를 생성, 이전에 반환했던 것과 같을 수 있다.
- **싱글턴 패턴**이나 **캐싱된 인스턴스를 반환할 때 사용**
- **대표적인 예시:** `Calendar.getInstance()`

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    
    private Singleton() {} // private 생성자

    public static Singleton getInstance() {
        return INSTANCE;
    }
}

Singleton singleton = Singleton.getInstance();
```

### create:

항상 새로운 인스턴스를 생성한다.
-  **매번 새로운 인스턴스를 생성할 때 사용**
- `new` 키워드와 동일한 의미

### newInstance:

- **리플렉션을 이용해 동적으로 객체를 생성할 때 사용**
- 일반적으로 `create()`보다는 덜 사용됨
- **대표적인 예시:** `Class.newInstance()`

### get\[Order Type]:
다른 타입의 인스턴스를 생성, 이전에 반환했던 것과 같을 수 있다.

### new\[Order Type]:
항상 다른 타입의 새로운 인스턴스를 생성한다.

# 실제 예제

### Optional의 of()

### List의 of()

### Integer의 valueOf()

입력값이 -128 ~ 127 범위의 정수라면 캐싱을 이용해 리턴한다.

# 정적 팩토리 메서드의 문제점

### private 생성자일 경우 상속 불가능
정적 팩토리 메서드로 클래스를 설계하면 생성자를 private 접근 제어자로 설정하게 되므로 상속을 이용한 확장이 불가능하다.
