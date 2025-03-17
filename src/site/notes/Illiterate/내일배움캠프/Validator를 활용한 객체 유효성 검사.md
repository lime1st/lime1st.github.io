---
{"dg-publish":true,"permalink":"/illiterate//validator/","tags":["test","junit"],"noteIcon":"","created":"2025-03-17T23:00:00","updated":"2025-03-18T03:16:17+09:00"}
---

## 객체의 유효성 검사 제약 조건을 검증하기 위한 단위 테스트

프로젝트의 테스트 코드 작성 중 Contoller에서 요청값을 검증하기 위해 NotBlank, NotNull 과 같은 애너테이션을 사용하는 데 이 제약 사항이 잘 작동하는 지 검증하고 싶었다.

> jakarta.validation.Validator 를 이용하면 간단하게 검증해 볼 수 있다.

`ValidatorFactory`를 생성하고, 이를 통해 `validator` 인스턴스를 가져온다.

```java
try (ValidatorFactory factory = Validation.buildDefaultValidatorFactory()) {  
    validator = factory.getValidator();  
}
```

`validate` 메서드를 이용해 객체의 값을 검증 한후 결과를 받아온다.

```java
Set<ConstraintViolation<CreateRequestDto>> violations = validator.validate(product);
```

`assertThat` 메서드를 활용해 결과 값을 확인해 볼 수 있다.

```java
private static Validator validator;  
  
@BeforeAll  
static void setUp() {  
    try (ValidatorFactory factory = Validation.buildDefaultValidatorFactory()) {  
        validator = factory.getValidator();  
    }  
}  
  
@Test  
@DisplayName("유효한 값으로 생성")  
void validateCompanyIdNotNull() {  
    var product = new CreateRequestDto(  
            UUID.randomUUID(),  
            UUID.randomUUID(),  
            "product1",  
            10000,  
            10  
    );  
    Set<ConstraintViolation<CreateRequestDto>> violations = validator.validate(product);  
  
    assertThat(violations).isEmpty();  
}
```