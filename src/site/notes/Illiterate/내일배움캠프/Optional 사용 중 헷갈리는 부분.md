---
{"dg-publish":true,"permalink":"/illiterate//optional/","tags":["java","optional"],"noteIcon":"","created":"2025-02-21T10:26:00","updated":"2025-03-12T02:50:06+09:00"}
---

튜터님과 코드 리뷰중 orElseThrow()와 ifPresent()를 사용에 대한 질문에 대답을 못해서 찾아보았다.
라이브러리를 사용할 때 사용 이유를 명확히 해야 겠다!
## orElseThrow() 와 ifPresent() 비교

#### orElseThrow()
- Optional에 **값이 있으면 반환**하고, **없으면 예외를 던짐**.
- 값이 반드시 존재해야 할 때, 값이 없으면 예외를 발생시켜 흐름을 중단하고 싶을 때 사용

#### ifPresent()
- Optional에 **값이 있으면** 제공된 **코드 블록(Consumer)을 실행**하고 없으면 아무 작업도 수행하지 않음.
- 값이 있으면 어떤 작업을 수행하고, 없을 때는 **특별한 조치가 필요하지 않을 때**, 값의 유무에 따라 **선택적 로직을 처리**할 때 사용

#### 비교

| **기능**       | orElseThrow()                           | ifPresent()                         |
| ------------ | --------------------------------------- | ----------------------------------- |
| **목적**       | 값이 없으면 예외를 던짐                           | 값이 있으면 작업 수행 (없으면 아무것도 안 함)         |
| **값의 반환**    | 내부 값을 반환 (T)                            | 반환값 없음 (void)                       |
| **동작 방식**    | 값이 있으면 반환, 없으면 Exception 발생             | 값이 있으면 Consumer 실행                  |
| **사용 사례**    | 값이 필수적일 때, 없는 경우 예외로 처리해야 할 때           | 값이 있으면 특정 작업을 수행하고 싶을 때             |
| **예외 발생 여부** | 값이 없으면 예외 발생                            | 예외 발생하지 않음                          |
| **코드 예시**    | name.orElseThrow(() -> new Exception()) | name.ifPresent(System.out::println) |

## 값의 존재만 확인 할 때는 exists...()

exists...()는 해당 필드 값이 존재하는지 **Boolean 값**으로 반환하기 때문에, **존재 여부만 확인할 때** ifPresent()보다 더 효율적이다.

#### 두 코드의 차이점

예를 들어 이메일 중복여부를 확인한다면...
|**특징**|findByEmail().ifPresent()|existsByEmail()|
|---|---|---|
|**목적**|회원 객체를 조회하고, 존재하면 예외 발생 | 회원 존재 여부만 확인하고, 존재하면 예외 발생 |
| **성능** | **SELECT 전체 열** (엔티티 전체 조회) | **SELECT 1** (존재 여부만 확인, 성능 우수) |
| **적합한 상황** | 조회한 객체를 사용해야 할 경우 | 객체가 필요 없고 **존재 여부**만 확인 |
| **리턴 타입** | Optional\<Member> | boolean |
| **JPA 메서드 예시** | Optional\<Member> findByEmail(String email) | boolean existsByEmail(String email)|

#### 왜 existsByEmail()이 더 좋은가

- findByEmail()은 DB에서 해당 레코드를 **전체 조회**한다.
- existsByEmail()은 **존재 여부만 확인**하므로 불필요한 데이터 로딩이 없다.
