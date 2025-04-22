---
{"dg-publish":true,"permalink":"/illiterate//feign-client-path/","tags":["feign"],"noteIcon":"","created":"2025-04-22T21:40:00","updated":"2025-04-22T21:41:28+09:00"}
---

MSA(Microservices Architecture) 환경에서 FeignClient를 사용할 때 요청이 전달되는 경로

두 번째, 세 번째 프로젝트를 MSA 로 개발을 하다보니 FeignClient 호출을 하는 경우가 많아졌는데 경로 관련 오류가 많이 생겨서 정리가 필요할 것 같다.

# 기본경로

1. **FeignClient 요청 생성**:
   - FeignClient는 인터페이스를 통해 HTTP 요청을 생성한다. 이 인터페이스는 Spring Cloud Feign을 사용하여 자동으로 구현된다.

2. **서비스 디스커버리**:
   - FeignClient는 Eureka, Consul 등의 서비스 디스커버리 툴을 통해 특정 서비스의 위치를 탐색하고 이를 기반으로 실제 요청이 어디로 보내질지를 결정한다. 직접 주소를 지정해 줄 수도 있다.

3. **요청 전송**:
   - FeignClient는 탐색된 서비스 주소를 바탕으로 HTTP 요청을 해당 서비스로 전송한다.

# Gateway를 통한 경로

1. **FeignClient 요청 생성**:
   - FeignClient는 인터페이스를 통해 요청을 생성

2. **서비스 디스커버리 및 Gateway 경유**:
   - FeignClient는 Eureka나 Consul 같은 서비스 디스커버리 툴을 통해 API Gateway의 주소를 탐색

3. **Gateway로 요청 전송**:
   - 요청은 API Gateway로 전송

4. **Gateway 처리**:
   - API Gateway는 요청을 수신한 후, 해당 요청을 적절한 마이크로서비스로 라우팅
   - 필요하면 인증, 로깅, 변환 등의 처리를 Gateway에서 수행될 수 있다.

5. **마이크로서비스 응답**:
   - 마이크로서비스는 Gateway를 통해 응답을 반환하고, FeignClient는 최종적으로 이 응답을 수신하여 클라이언트에게 전달한다.

# 요약
- **직접 경로**: FeignClient -> Service Discovery -> Target Service
- **Gateway 경유**: FeignClient -> Service Discovery -> API Gateway -> Target Service

---
[[Illiterate/내일배움캠프/FeignClient - Patch Method\|FeignClient - Patch Method]]