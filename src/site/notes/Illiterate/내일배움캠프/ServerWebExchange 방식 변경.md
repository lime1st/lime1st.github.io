---
{"dg-publish":true,"permalink":"/illiterate//server-web-exchange/","tags":["msa"],"noteIcon":"","created":"2025-02-11T13:01:00","updated":"2025-02-11T14:04:38+09:00"}
---

### 내가 사용한 주요 라이브러리 버전

> 스프링부트: 3.4.0
> jjwt: 0.12.6

### error

msa 강의 마지막 실습 작성 중에 gateway에서 jwt 토큰을 검증 후 토큰에서 claim을 얻어와 header에 데이터를 넣는 과정에서 계속 에러가 났다.

```
DefaultHandlerExceptionResolver : Resolved [org.springframework.web.bind.MissingRequestHeaderException: Required request header 'X-User-Id' for method parameter type String is not present]
```

코드를 비교해 봐도 틀린 부분은 없었는데 계속 헤더 값이 넣어지지 않아서 GPT에 코드 검증을 시켜봤다.


> [!NOTE] ChatGPT 답변
> **Spring Boot 및 관련 라이브러리(Spring WebFlux, JJWT 등)의 버전이 업그레이드되면서 동작 방식이 변경된 것**입니다.
> **exchange.getRequest().mutate().header(...).build(); 방식의 변경**
> 	기존에는 exchange.getRequest().mutate()로 새로운 요청을 만들면 자동으로 적용되었지만, **새로운 Spring Boot(WebFlux)에서는 exchange 자체를 업데이트해야 적용됨**.
> **Spring Boot 3.x 이상에서는 exchange.getRequest().mutate() 방식이 바뀌었고, 변경된 요청을 exchange에 적용해야 함.**


### 코드 수정

- 토큰 검증하는 부분에서 새로운 exchange 를 반환하도록 변경했다.

```java
@Override  
public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {  
    String path = exchange.getRequest().getURI().getPath();  
  
    // 인증이 필요 없는 경로는 그냥 통과  
    if (path.equals("/auth/signIn") || path.equals("/auth/signUp")) {  
        return chain.filter(exchange);  
    }  
  
    // Authorization 헤더에서 토큰 추출  
    String token = extractToken(exchange);  
  
    if (token == null) {  
        exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);  
        return exchange.getResponse().setComplete();  
    }  
  
    // 토큰 검증 및 헤더 추가 후 체인 실행  
    return validateToken(token, exchange)  
            .flatMap(chain::filter)  
            .onErrorResume(e ->{  
                exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);  
                return exchange.getResponse().setComplete();  
            });  
}

private Mono<ServerWebExchange> validateToken(String token, ServerWebExchange exchange) {  
    try {  
        SecretKey key = Keys.hmacShaKeyFor(Decoders.BASE64URL.decode(secretKey));  
        Jws<Claims> claimsJws = Jwts.parser()  
                .verifyWith(key)  
                .build()  
                .parseSignedClaims(token);  
  
        log.info("######payload :: {}", claimsJws.getPayload());  
  
        Claims claims = claimsJws.getPayload();  
  
        // 안전한 타입 변환  
        String userId = claims.get("user_id", String.class);  
        String role = claims.get("role", String.class);  
  
        if (userId == null || role == null) {  
            log.warn("JWT is missing required claims");  
            return Mono.error(new RuntimeException("Invalid JWT token"));  
        }  
  
        // 새로운 요청 객체 생성  
        ServerHttpRequest mutatedRequest = exchange.getRequest().mutate()  
                .header("X-User-Id", userId)  
                .header("X-Role", role)  
                .build();  
  
        // 추가적인 검증 로직 (예: 토큰 만료 여부 확인 등)을 여기에 추가할 수 있습니다.  
  
        // 변경된 요청을 포함한 새로운 exchange 반환  
        return Mono.just(exchange.mutate().request(mutatedRequest).build());  
    } catch (Exception e) {  
        log.error("JWT validation fail", e);  
        return Mono.error(new RuntimeException("Invalid JWT Token", e));  
    }  
}
```

### 결론

실습하거나 공부할 때는 되도록 버전을 맞춰서 하자!~~라고 하면서 자꾸 최신버전으로 패키지를 만들게 된다.~~

