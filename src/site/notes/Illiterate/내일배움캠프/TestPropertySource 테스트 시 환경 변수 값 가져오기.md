---
{"dg-publish":true,"permalink":"/illiterate//test-property-source/","tags":["test","property"],"noteIcon":"","created":"2025-02-24T22:34:00","updated":"2025-02-24T22:36:59+09:00"}
---

테스트 코드를 작성하고 실행을 할 때 application.yml 파일에 필요한 환경변수를 가져오지 못하는 문제가 발생했다.

```yml
jwt:
  secret: ${JWT_SECRET}
```

단순히 테스트 실행 설정에서 환경변수를 직접 전달하면 해결이 될 듯 하여 적용하고 실행을 했다.

![Screenshot 2025-02-24 at 1.12.28 am.png](/img/user/98.%20Attach/Screenshot%202025-02-24%20at%201.12.28%20am.png)

테스트 메서드에만 적용하면 테스트 메서드를 추가할 때마다 따로 설정을 해 주어야 해서 클래스 실행에 추가하고 모든 테스를 한 번에 실행하는 방식으로 테스트를 진행했다.

이렇게 하면 로컬에만 환경변수 설정이 적용되므로 github action 등 ci/cd를 활용할 때 문제가 발생한다. 다음의 설정으로 환경변수로 설정했던 값을을 임의의 값으로 넣어줄 수 있다.

```java
@TestPropertySource(properties = {
	"속성명=값",
	...
})
```

location = "classpath:application-test.yml" 이런식으로 값을 넣은 파일을 따로 설정해서 적용할 수도 있다.

