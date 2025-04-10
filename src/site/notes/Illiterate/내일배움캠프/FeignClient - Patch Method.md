---
{"dg-publish":true,"permalink":"/illiterate//feign-client-patch-method/","tags":["feign","patch"],"noteIcon":"","created":"2025-04-10T21:37:00","updated":"2025-04-10T21:59:45+09:00"}
---

FeignClient로 PatchMethod 요청을 하면

```
feign.RetryableException: Invalid HTTP method: PATCH executing PATCH
```

이런 예외가 발생한다.

OpenFeign은 `@PatchMapping`을 직접 지원하지만, 기본적으로 사용하는 HTTP 클라이언트(HttpURLConnection)는 PATCH 메서드를 지원하지 않아 발생하는 문제였다.

이를 해결하기 위해 FeignClient에서 사용하는 HTTP 클라이언트를 변경해 주어야 한다.

Apache HttpClient를 사용하거나 ok-http를 사용하면 된다.

```gradle
implementation 'io.github.openfeign:feign-okhttp:13.5'
// 또는
implementation 'io.github.openfeign:feign-httpclient:13.5'
```

의존성에 맞는 HttpClient로 변경해주는 설정도 추가해야 한다.
```java
@Configuration
public class FeignHttpConfig {

    @Bean
    public Client feignClient() {
        return new ApacheHttpClient();
    }
}

@Configuration  
public class FeignHttpConfig {  
  
    @Bean  
    public Client feignClient() {  
        return new OkHttpClient();  
    }  
}
```

FeignClient에 변경된 HttpClient를 사용하도록 configuration 속성으로 지정해 주면 된다.

```java
@FeignClient(name = "service-name", configuration = FeignHttpConfig.class)  
public interface ServiceClient {  
  
    @PatchMapping("/{id}")  
    void update(
	    @PathVariable Long id,  
        @RequestBody UpdateRequestDto request  
    );  
}
```

이렇게 설정해 주면 Patch 메서드 요청이 적용이 될 것이다...만

우리 프로젝트는 MSA 환경에서 진행되고 있었고 서비스 디스커버리로 eureka를 사용 중이었는데 위의 설정으로 진행하면 계속 예외가 발생했다.

```
feign.RetryableException: service-name: nodename nor servname provided, or not known executing
```

다음과 같은 문제라고 하는데 HttpClient 설정을 변경하기 전 Get, Post 등으로 테스트 할 때에는 잘 적용이 되었기 때문에 이유를 찾는데 애를 먹은 것 같다.

> Feign 클라이언트가 ticket-service라는 서비스 이름을 DNS 또는 서비스 레지스트리(Eureka)에서 해석하지 못해 발생한 문제입니다. 이는 Apache HttpClient로 전환한 후 PATCH 메서드 자체의 문제는 해결되었을 가능성이 있지만, Eureka와의 서비스 디스커버리 설정에 문제가 있음을 나타냅니다.

몇 번의 삽질 끝에 url 을 지정해주었더니 해결 되었다......아 나의 시간...

```java
@FeignClient(name = "service-name", url = "localhost:0000", configuration = FeignHttpConfig.class)
public interface Client {
    //..
}
```

---
[공식문서](https://docs.spring.io/spring-cloud-openfeign/docs/current/reference/html/)
