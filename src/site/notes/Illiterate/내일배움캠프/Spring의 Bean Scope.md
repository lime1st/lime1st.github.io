---
{"dg-publish":true,"permalink":"/illiterate//spring-bean-scope/","tags":["spring","bean","scope"],"noteIcon":"","created":"2025-03-12T10:25:00","updated":"2025-03-12T23:52:17+09:00"}
---

## 스코프 Scope

빈을 생성하고 수명 주기를 관리하는 방식

#### 싱글톤 singleton

- 스프링에서 빈의 기본 스코프
- 싱글톤 패턴에서 말하는 싱글톤과는 다르다. 스프링에서는 고유한 이름에 대해 동일한 인스턴스를 반환한다. 스프링 컨텍스트에서 인스턴스의 이름이 다르다면 동일한 클래스의 인스턴스라도 여러개 가질 수 있다.
- 싱글톤 빈의 스코프는 앱의 여러 컴포넌트가 하나의 객체 인스턴스를 공유할 수 있다고 가정한다. 따라서 싱글톤 빈은 불변이어야 한다. -> 생성자 주입 방식은 필드를 final로 지정할 수 있어 빈을 불변으로 만들 수 있다.
- 가변 싱글톤 빈을 사용하려면 스레드 동기화를 사용하여 동시성이 있는 concurrent 빈이 되도록 직접 만들어야 한다.
- **race condition** 여러 스레드가 싱글톤 빈에 액세스하면 동일한 인스턴스에 액세스하게된다. 동시성을 위해 설계해야 한다.

#### @Lazy

- 대부분의 경우 스프링은 컨텍스트를 초기화할 때 모든 싱글톤 빈을 생성하는데, 이것이 스프링의 기본 동작이다. -> 즉시 인스턴스 생성 방식이라고도 한다. eager instantiation
- Lazy instantiation 방식은 누군가 처음 이 빈을 참조할 때만 스프링에 빈을 생성하도록 지시한다.
- 기본적인 즉시 eager 생성 방식의 장점이 더 많으므로 유불리를 따져 적용해야 한다.

#### 프로토타입 prototype

- @Bean @Scope(BeanDefinition.SCOPE_PROTOTYPE) 을 동시에 적용하여 만든다.
- 스테레오타입과도 함께 사용할 수 있다. @Repository @Scope
- 프로토타입을 사용하면 빈의 기본 생성방식인 싱글톤 방식과 다르게 빈을 요청하는 각각의 스레드가 서로 다른 인스턴스를 얻기 때문에 동시성 문제가 발생하지 않는다. 따라서 변경 가능한(mutable) 프로토타입 빈을 정의하는 것은 문제가 되지 않는다.
- 프로토타입 스코프의 빈을 싱글톤 스코프의 빈에 주입할 때는 주의해야 한다. 싱글톤 빈은 한 번만 생성되므로 결과적으로 프로토타입 빈도 1번만 생성되게 된다. 이때는 context.getBean 메서드를 호출하여 항상 새로운 인스턴스를 제공받아야 한다.

## 웹 애플리케이션에 적용되는 추가 스코프

#### 요청 request 스코프

- 스프링은 각 HTTP 요청에 대해 빈 클래스의 인스턴스를 생성한다. 이 인스턴스는 해당 HTTP 요청에서만 존재한다.
- 해당 빈에 @RequestScope 애너테이션을 적용한다.

#### 세션 session 스코프

- 스프링은 인스턴스를 생성하고 전체 HTTP 세션 동안 서버의 메모리에 이 인스턴스를 유지한다. 스프링은 컨텍스트에서 이 인스턴스를 클라이언트의 세션과 연결한다. 동일한 클라이언트의 여러 요청에서 동일한 인스턴스를 공유할 수 있다.
- @SessionScope

#### 애플리케이션 application 스코프

- 인스턴스는 앱의 컨텍스트에서 고유하며, 앱이 실행되는 동안 사용할 수 있다. 해당 빈에 대해 애플리케이션 전체에서 하나의 인스턴스만 존재한다.
- @ApplicationScope

---
출처: [스프링 교과서](https://product.kyobobook.co.kr/detail/S000213355775)

[[Illiterate/내일배움캠프/FetchType.LAZY\|FetchType.LAZY]]