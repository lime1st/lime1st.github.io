---
{"dg-publish":true,"permalink":"/illiterate//dto-entity-data-type/","tags":["type","#dto","#entity"],"noteIcon":"","created":"2025-03-21T23:52:00","updated":"2025-03-23T17:47:50+09:00"}
---

## 1. 개요

DTO를 만들 때 primitive type과 wrapper type 구분 없이 생성하다가 문득 왜?라는 생각이 들었다.
더불어 엔티티에는 어떤 기준을 적용하는 지도..

요청 객체(Request DTO)와 응답 객체(Response DTO)에서 **primitive type(`int`, `long`)과 wrapper class(`Integer`, `Long`) 중 어떤 것을 사용하는 것이 좋은가?**

## `int` vs `Integer` 선택 기준

| 기준               | `int` (Primitive Type) | `Integer` (Wrapper Class) |
| ---------------- | ---------------------- | ------------------------- |
| **null 허용 여부**   | `null` 불가능 (기본값 `0`)   | `null` 가능                 |
| **객체 비교**        | 빠른 비교 (== 사용 가능)       | `equals()` 비교 필요          |
| **메모리 사용**       | 상대적으로 적음               | 더 많은 메모리 사용               |
| **JPA, JSON 처리** | `null`을 명확하게 표현할 수 없음  | `null` 값 처리 가능            |

## 2. 요청 객체(Request DTO)에서의 선택

**일반적으로 `Integer`, `Long` 같은 Wrapper 타입을 선호**  

- 요청 값이 `null`일 수도 있음 (`int`는 `null`을 허용하지 않음)
- 클라이언트가 해당 값을 보내지 않았는지(`null`) 아니면 0을 보낸 건지 구별할 필요가 있음
- JSON 파싱 시 `int`보다 `Integer`가 더 자연스럽게 처리됨

#### 예시 (좋은 예)

```java
public record OrderCreateRequest(
    Integer productId, // 클라이언트가 값을 생략할 수도 있음
    Integer quantity
) {}
```

- 클라이언트가 `quantity` 값을 생략하면 `null`로 처리 가능
- `@NotNull` 등의 검증 애너테이션과 함께 사용할 수 있음

## 3. 응답 객체(Response DTO)에서의 선택

**응답 객체에서는 primitive type(`int`, `long`)을 선호하는 경우가 많음**  

- 응답 값은 `null`이 아니라 의미 있는 기본값을 가져야 하는 경우가 많음
- 클라이언트에서 사용하기 편리함 (null 체크 필요 없음)

#### 예시 (좋은 예)

```java
public record OrderResponse(
    int orderId,
    int quantity
) {}
```

- 응답 값이 항상 존재해야 한다면 **primitive type(`int`)을 사용하는 것이 적절**하다.
- `null`이 의미 있는 경우(예: 값이 없을 수도 있음)는 `Integer`를 사용할 수도 있음

## 4. 결론

| 객체 유형      | 추천 타입                             | 이유                          |
| ---------- | --------------------------------- | --------------------------- |
| **요청 DTO** | `Integer`, `Long` (Wrapper Class) | `null` 허용 가능, 값이 비어 있을 수 있음 |
| **응답 DTO** | `int`, `long` (Primitive Type)    | 기본값이 필요하고 `null`이 없도록 보장    |

- **요청 객체(Request)**: `Integer`, `Long` 같은 Wrapper 타입을 사용해서 `null`을 처리할 수 있도록
- **응답 객체(Response)**: `int`, `long` 같은 Primitive 타입을 사용해서 `null`이 없도록 보장

---

**엔티티(Entity) 객체에서 `int` vs `Integer`(Primitive vs Wrapper) 선택 기준**은 **데이터베이스와 직접 매핑되는 객체**라서 `null` 처리, JPA 동작 방식 등을 고려해야한다.

## 1. 엔티티에서 `int` vs `Integer` 선택 기준

| 기준                         | `int` (Primitive Type)                | `Integer` (Wrapper Class) |
| -------------------------- | ------------------------------------- | ------------------------- |
| **null 허용 여부**             | `null` 불가능 (`0` 기본값)                  | `null` 가능                 |
| **DB 매핑 (JPA)**            | `null` 저장 불가                          | `null` 저장 가능              |
| **자동 증가 (Auto Increment)** | 사용 불가능 (`@GeneratedValue`와 함께 사용 불가능) | 사용 가능                     |
| **조회 시 값이 없을 때**           | `null` 대신 `0`으로 채워짐                   | `null` 유지 가능              |

## 2. ID 필드에서는 반드시 `Wrapper 타입` 사용

**기본 키(`@Id`)는 `Integer`, `Long` 같은 Wrapper 타입을 사용해야 함.**  

- **`int`는 `null`을 허용하지 않아서** JPA에서 `@GeneratedValue`로 자동 생성할 때 문제가 발생함.
- **JPA는 `null`인 경우 새 엔티티로 판단하고 ID를 생성**하는데, `int`는 기본값 `0`이 있어서 새 엔티티로 인식되지 않을 수 있음.

#### 예시 (좋은 예)

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id; // Long을 사용해야 함

    private Integer quantity; // Integer 사용 (null 허용 가능)
}
```

#### 잘못된 예 (primitive type 사용)

```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id; // JPA가 자동 증가 ID를 부여할 때 문제가 발생할 수 있음

    private int quantity; // quantity가 `null`이어야 할 경우를 처리 못 함
}
```

## 3. 일반적인 숫자 필드에서는 상황에 따라 다름

#### `int`, `long`을 써도 되는 경우

기본값이 **항상 존재해야 하는 필드**  
`null`을 허용할 필요가 없음  
예: **재고 수량, 가격, 포인트 등**

```java
private int stockQuantity;
private long totalSales;
private double discountRate;
```

- **이유**: `stockQuantity` 같은 필드는 기본값이 `0`이어야 의미가 명확함.
- **주의**: JPA에서 `null`이 들어와도 `0`으로 초기화됨.

#### `Integer`, `Long`을 써야 하는 경우

필드 값이 **필수 값이 아닐 때**  
`null`을 의미 있게 사용할 때  
예: **할인율이 없을 수도 있음, 주문 취소 사유가 없을 수도 있음**

```java
private Integer discountRate; // 할인율이 없을 수도 있음
private Long canceledByUserId; // 주문 취소자가 없을 수도 있음
```

- **이유**: `null`을 구분해야 하는 경우라면 Wrapper 타입을 써야 함.

## 4. 정리

| 필드 유형            | 추천 타입                       | 이유                                 |
| ---------------- | --------------------------- | ---------------------------------- |
| **기본 키 (`@Id`)** | `Long`, `Integer` (Wrapper) | `@GeneratedValue`를 위해 `null` 허용 필요 |
| **필수 숫자 필드**     | `int`, `long` (Primitive)   | 기본값이 항상 존재해야 할 때                   |
| **옵션 숫자 필드**     | `Integer`, `Long` (Wrapper) | `null`을 저장할 가능성이 있을 때              |

## 5. 최종 결론

- **ID 필드는 `Long`(Wrapper) 사용**
- **필수 값은 `int`, `long` 사용 (기본값이 `0`이 필요할 때)**
- **옵션 값은 `Integer`, `Long` 사용 (null을 의미 있게 사용할 때)**

**즉, `nullable`이면 `Integer`, `Long`, 항상 값이 있어야 하면 `int`, `long`을 쓰는 게 좋다!**
