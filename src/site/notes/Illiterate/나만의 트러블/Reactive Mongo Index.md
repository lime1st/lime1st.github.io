---
{"dg-publish":true,"permalink":"/illiterate//reactive-mongo-index/","tags":["mongoDB","testcontainer","test"],"noteIcon":"","created":"2025-05-06T21:30:00","updated":"2025-05-06T21:30:18+09:00"}
---

잘 작동했던 테스트 코드가 갑자기 에러가 생겼다.

```
java.lang.IllegalStateException: Indexes are not unique
```

## 문제 원인

찾아보니 다음과 같은 이유가 있었다.

1. `@Indexed(unique = true)`가 적용되어 있어도 **MongoDB에 자동으로 인덱스를 생성하지 않는 경우**가 있음  
    → Spring Boot는 **인덱스 생성을 명시적으로 활성화하지 않으면**, 도메인에 `@Indexed`가 있어도 자동으로 생성되지 않을 수 있습니다.
 
2. `ReactiveMongoTemplate` 환경에서는 인덱스 생성 타이밍이 느려서 타이밍 문제로 실패할 수 있음

## 해결 방법

 인덱스 자동 생성 활성화

`application.yml` 또는 `application.properties`에 다음 설정 추가:

```yaml
spring:
  data:
    mongodb:
      auto-index-creation: true
```

> 📝 이 설정이 없으면 `@Indexed`가 붙어 있어도 인덱스가 생성되지 않습니다! 라고 한다!

---
스프링 부트에는 다양한 설정값들이 있다. 이제 뭔가 안되면 설정값 부터 찾아봐야하나...

