---
{"dg-publish":true,"permalink":"/illiterate//request-context-holder/","tags":["requestcontextholder","inheritablethreadlocal"],"noteIcon":"","created":"2025-03-29T14:51:00","updated":"2025-04-18T10:40:52+09:00"}
---

두 번째 프로젝트에서 AuditorAware 를 구현하면서 `RequestContextHolder`를 사용했는데 튜터님이 `RequestContextHolder`는 내부적으로 `ThreadLocal`을 사용해서 비동기 로직에서 해당 홀더를 접근하지 못할 거라고 해서 해결 방안을 찾아 보았다.

## 1. SecurityContext 에서 가져오기

`SecurityContextHolder`에서 가져오면 간단하게 해결 될 수 있으나 MSA 환경에는 적합하지 않다.
우리 프로젝트도 인증/인가는 auth-service에서 담당하고 나머지 프로젝트는 security 의존성을 제거하고 header 로 처리 하였다.

## 2. `InheritableThreadLocal` 사용

기본적으로 `ThreadLocal`은 새로운 스레드에서 값을 상속받지 **않지만**, `InheritableThreadLocal`을 활성화하면 자식 스레드에서 값을 유지할 수 있다.

```java
@PostConstruct
public void enableInheritableThreadLocal() {
    RequestContextHolder.setRequestAttributes(RequestContextHolder.getRequestAttributes(), true);
}

```

- true를 설정하면 `InheritableThreadLocal`이 활성화되어 비동기 스레드에서도 `RequestContextHolder`를 참조할 수 있다.

> MSA 환경에서는 비동기 호출이 많기 때문에 `RequestContextHolder`를 직접 쓰는 것보다 요청 정보를 명시적으로 전달하는 방식이 더 안전하다.


---
[[Illiterate/내일배움캠프/JpaAuditing 적용\|JpaAuditing 적용]]
[[Illiterate/내일배움캠프/비동기(@Acync) 처리시 주의\|비동기(@Acync) 처리시 주의]]