---
{"dg-publish":true,"permalink":"/illiterate//validator/","tags":["test","junit"],"noteIcon":"","created":"2025-03-17T23:00:00","updated":"2025-03-17T23:01:13+09:00"}
---

## 객체의 유효성 검사 제약 조건을 검증하기 위한 단위 테스트

javax.validation.Validator를 활용해 객체의 유효성 검사를 위한 제약 조건을 테스트 할 수 있다.

객체의 필
```java
@NotBlank
@NotNull
@Positive
...
```