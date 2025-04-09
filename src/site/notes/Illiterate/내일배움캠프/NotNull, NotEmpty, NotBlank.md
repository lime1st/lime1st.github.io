---
{"dg-publish":true,"permalink":"/illiterate//not-null-not-empty-not-blank/","tags":["validate"],"noteIcon":"","created":"2025-04-09T23:42:00","updated":"2025-04-10T02:44:37+09:00"}
---

컨트롤러에서 유효성 검사를 진행할 때, 매번 헷갈려서 정리를 하게 됐다.

`@NotEmpty`, `@NotNull`, `@NotBlank`는 **유효성 검증 어노테이션**이지만, **적용 대상**과 **검사 기준**이 다르다!

---

## 차이점

|어노테이션|적용 대상|허용값|검사 기준|예시|
|---|---|---|---|---|
|`@NotNull`|모든 타입|`""`, `" "` 등은 허용|**null만 허용 안 함**|null이면 에러, 빈 문자열은 통과|
|`@NotEmpty`|String, Collection, Map, Array|`" "`(공백) 허용|**null 또는 비어 있으면 에러**|`[]`, `""`이면 에러|
|`@NotBlank`|`String` 전용|공백 `" "` 도 에러|**null, "", 공백만 있는 문자열 금지**|`" "`도 에러|

---

## 언제 어떤 걸 쓰는 것이 좋은가?

|상황|추천 어노테이션|이유|
|---|---|---|
|필드가 null인지 아닌지만 검사하면 되는 경우|`@NotNull`|값이 존재하기만 하면 됨|
|리스트가 null이 아니고 비어있지도 않아야 할 때|`@NotEmpty`|`List.size() > 0` 조건|
|문자열이 null이 아니고 공백도 아닌 값이어야 할 때|`@NotBlank`|유저 이름, 제목, 내용 등 입력 필수 문자열 필드|

---

## 예제 코드

```java
public class CreateUserRequest {

    @NotNull
    private String id; // null만 막고 빈 문자열은 허용

    @NotEmpty
    private List<String> roles; // null, [] 모두 불허

    @NotBlank
    private String username; // null, "", " " 모두 불허
}
```

---

## 기타

- `@NotBlank`는 내부적으로 `@NotNull`과 `String.trim().length() > 0` 체크를 함께 한다.
    
- 필드가 `String`일 때는 거의 대부분 `@NotBlank` 사용 (공백만 있는 입력도 무시하려고).
    
- `@NotEmpty`는 컬렉션이나 배열에 적합
    
- `@NotBlank`는 `String` 타입에만 사용할 수 있다.
    
- `UUID`, `Long`, `Integer` 같은 타입에는 **무조건 `@NotNull`** 사용
