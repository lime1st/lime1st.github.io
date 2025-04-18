---
{"dg-publish":true,"permalink":"/illiterate//json-path/","tags":["json"],"noteIcon":"","created":"2025-04-18T10:13:00","updated":"2025-04-18T10:13:55+09:00"}
---

# 기본 개념

| 표현식  | 설명                            | 예시                                   |
| ---- | ----------------------------- | ------------------------------------ |
| `$`  | 루트 노드 (JSON 전체의 시작)           | `$` → 전체 객체                          |
| `@`  | 현재 노드 (테스트에서는 전체 JSON 응답을 의미) | `@.result.title`                    |
| `.`  | 자식 노드 접근                      | `$.result.title`                     |
| `[]` | 배열 접근 또는 필드 이름                | `$.result[0].title` 또는 `$['result']` |
| `*`  | 와일드카드 (모든 항목)                 | `$.result[*].title` (배열의 모든 title)   |

# `$` vs `@` 차이점

|기호|의미|주로 사용되는 곳|
|---|---|---|
|`$`|**절대 경로** (루트에서부터 시작)|일반 JSON Path 라이브러리, Jackson 등|
|`@`|**현재 노드 기준 경로**|`JsonContentAssert` 등 테스트 프레임워크 안에서 사용|

> **Spring의 `JsonContentAssert` 계열에서는 주로 `@` 사용.**  
> 반면, 일반 JSONPath 라이브러리나 JavaScript JSONPath 문법에서는 `$` 사용.

---

# 예제 JSON

```json
{
  "code": 2000,
  "result": {
    "id": "abc-123",
    "type": "EMAIL",
    "title": "hello"
  },
  "message": "success"
}
```

### JSONPath 예시

| JSONPath         | 설명                          | 결과값       |
| ---------------- | --------------------------- | --------- |
| `$.result.type`  | 루트에서 시작해 `result.type` 접근   | `"EMAIL"` |
| `@.result.title` | 현재 JSON에서 `result.title` 접근 | `"hello"` |
| `$.code`         | 루트의 `code` 필드               | `2000`    |

---

# 테스트에서 주의할 점

Spring `JsonContentAssert` 같은 테스트 유틸에서:

```java
assertThat(jsonContent)
    .extractingJsonPathStringValue("@.result.notificationType")
    .isEqualTo("EMAIL");
```

> `@`가 루트처럼 동작함. 테스트 내부에서는 `@ == $`

---

# 배열 다루기

예: 응답이 배열이라면

```json
{
  "data": [
    { "title": "title1" },
    { "title": "title2" }
  ]
}
```

|JSONPath|결과|
|---|---|
|`$.data[0].title`|`"title1"`|
|`$.data[*].title`|`["title1", "title2"]`|

---

# 조건 필터 (`?()`)

### 문법

```json
$[?(@.속성 == '값')]
```

- `?()` → 조건 필터
    
- `@` → 현재 배열 항목을 가리킴
    
- \==, `!=`, `<`, `>`, `<=`, `>=` 등의 연산자 사용 가능
    

### 예제

```json
{
  "note": [
    {
      "id": "1",
      "type": "EMAIL",
      "title": "email title"
    },
    {
      "id": "2",
      "type": "SMS",
      "title": "sms title"
    }
  ]
}
```

### 특정 타입 필터링

```json
$.note[?(@.type == 'EMAIL')]
```

결과:

```json
[
  {
    "notificationId": "1",
    "notificationType": "EMAIL",
    "title": "email title"
  }
]
```

---

### 필터 내 다중 조건

##### AND 조건

```json
$.note[?(@.type == 'EMAIL' && @.title == 'email title')]
```

##### OR 조건

```json
$.note[?(@.type == 'EMAIL' || @.type == 'SMS')]
```

---

### 값만 추출하고 싶을 때

```json
$.note[?(@.type == 'EMAIL')].title
```

결과: `["email title"]`

---

### 테스트에 적용한다면?

Spring의 `JsonContent` 테스트:

```java
assertThat(jsonContent)
    .extractingJsonPathStringValue("@.note[?(@.type == 'EMAIL')].title")
    .isEqualTo("email title");
```

※ 배열이면 `extractingJsonPathArrayValue()` 를 쓰는 게 더...
