---
{"dg-publish":true,"permalink":"/illiterate//comparator/","tags":["comparator"],"noteIcon":"","created":"2025-04-18T10:29:00","updated":"2025-04-18T10:30:03+09:00"}
---

Service 클래스의 테스트를 위해 StubRepository 클래스를 만드는 중 Pageable을 이용한 정렬 부분을 구현하던 중 계속 오류가 났다.

`Comparator.comparing(...)`의 핵심은 **리턴하는 값이 `Comparable`을 구현한 타입**이어야 한다는 것이다. `String`, `UUID`, `Enum` 등 `Comparable<T>`를 구현한 타입이면 비교가 가능하다.
그러나 한 compaing에서 다른 값을 리턴하면 비교가 불가능해 진다.

---

# 잘 작동하는 경우:

```java
Comparator.comparing(
    note -> {
        if (sort.getProperty().equalsIgnoreCase("title")) {
            return note.getTitle();  // String
        } else {
            return note.getMessage();  // String
        }
    }
);
```

이건 잘 작동하는 이유가, `title`과 `message` 모두 `String` 타입이고, `String`은 `Comparable<String>`을 구현하고 있기 때문

---

# 안 되는 경우: 타입이 달라지거나 null이 생기는 경우

```java
Comparator.comparing(
    note -> {
        if (sort.getProperty().equalsIgnoreCase("notificationType")) {
            return note.getType();  // Enum
        } else {
            return note.getId();  // UUID
        }
    }
);
```

이렇게 작성하면 두 경로에서 서로 다른 타입(`type`은 `Enum`, `id`는 `UUID`)을 리턴하므로 **컴파일러 입장에서는 리턴 타입을 유추할 수 없다**.

---

# 해결 방법 1: 타입 분기

경우에 따라 `Comparator` 자체를 분기해서 명확한 타입으로 지정

```java
Comparator<Note> comparator;

if (sort.getProperty().equalsIgnoreCase("type")) {
    comparator = Comparator.comparing(Note::getType);
} else if (sort.getProperty().equalsIgnoreCase("id")) {
    comparator = Comparator.comparing(Note::getId);
} else {
    comparator = Comparator.comparing(Note::getTitle);
}
```

이렇게 하면 각 `comparing`에 들어가는 타입이 명확해서 문제 없음.

---

# 해결 방법 2: 캐스팅을 이용한 통합

```java
Comparator.comparing(note -> 
    (Comparable) (
        sort.getProperty().equalsIgnoreCase("type")
            ? note.getType()
            : note.getId()
    )
);
```

이건 타입 오류는 피할 수 있지만 ==**타입 안정성**==이 깨지기 때문에 분기하는 방식이 가장 안전하고 유지보수에도 좋다

---

# 기타

| 타입                 | Comparable 구현 여부                   |
| ------------------ | ---------------------------------- |
| `String`           | 구현함                                |
| `UUID`             | 구현함                                |
| `Enum`             | 구현함 (모든 Enum은 기본적으로 name 기준 비교 가능) |
| `List`, `Object` 등 | 직접 비교 로직 필요                        |
