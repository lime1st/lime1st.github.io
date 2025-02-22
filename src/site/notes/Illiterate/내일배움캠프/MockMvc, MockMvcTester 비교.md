---
{"dg-publish":true,"permalink":"/illiterate//mock-mvc-mock-mvc-tester/","tags":["mockmvc","mockmvctester"],"noteIcon":"","created":"2025-02-22T11:34:00","updated":"2025-02-22T11:34:52+09:00"}
---

# MockMvc

`MockMvc`는 **Spring MVC 컨트롤러를 테스트하는 전통적인 방법**으로, `perform()` 메서드를 사용해 요청을 보내고 `andExpect()`로 검증한다.

### 특징

- `MockMvc`는 **Http 요청을 직접 시뮬레이션**하는 방식
- `andExpect()`로 응답을 검증
- `ResultActions`를 사용해 체이닝 가능
- JSON 응답을 다룰 때 `jsonPath()`를 활용해야 함

### 사용 예제

```java
@Test
void testMockMvc() throws Exception {
    mockMvc.perform(post("/api/sign-up")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(bob))
            .with(csrf()))
        .andExpect(status().isBadRequest()) 
        .andExpect(jsonPath("$.message").value(containsString("이메일 형식")));
}
```

`mockMvc.perform()`을 사용하여 **요청을 실행한 후 `andExpect()`를 체이닝**해서 검증.

---

# MockMvcTester

`MockMvcTester`는 **Spring Boot 3.2부터 도입된 새로운 테스트 API**로, `MockMvc`보다 **더 직관적인 API 디자인을 제공**한다고 한다.

### 특징

- `assertThat()` 스타일로 검증 가능 (JUnit + AssertJ 조합)
- `bodyJson()`을 직접 사용할 수 있어 JSON 응답을 쉽게 다룰 수 있음
- `jsonPath()` 없이 `as(Class<T>)`로 JSON을 `Map` 또는 DTO로 변환 가능
- 보다 **Fluent API 스타일**로 작성 가능

### 사용 예제

```java
assertThat(mvc.post().uri("/api/sign-up")
        .contentType(MediaType.APPLICATION_JSON)
        .content(objectMapper.writeValueAsString(bob))
        .with(csrf())
).hasStatus(HttpStatus.BAD_REQUEST)
        .bodyJson().asString().contains("이메일 형식");
```

`MockMvc`보다 코드가 더 간결하고 직관적인지는 모르겠다. 아무래도 자주 사용해야 더 직관적으로 다가오지 않을까...

---

assertThat으로 감싸서 사용한다는 부분이 다소 어색하게 느껴졌다.
bodyJson().asString()으로 Json을 확인 하는 점은 편한 것 같다.
