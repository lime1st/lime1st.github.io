---
{"dg-publish":true,"permalink":"/illiterate//jwt-claim-vs-subject/","tags":["jwt"],"noteIcon":"","created":"2025-02-17T17:42:00","updated":"2025-03-12T02:49:18+09:00"}
---

프로젝트에서 맡은 인증 / 인가 부분을 구현하다가 어디는 claim()을 사용하는 예제가 있고 subject()를 사용하는 예제가 있어 뭐가 다른지 찾아 보았다.

 **JWT 표준(Registered Claims) 준수 여부**

# claim() vs subject()의 차이점

### claim()

• 커스텀(claim) 데이터를 추가할 때 사용
• JWT ==표준에서 정의하지 않은 데이터==를 넣을 수 있다.
	• 예: 사용자 역할, 권한 정보, 사용자 ID 등

### subject()

• **Registered Claims(등록된 클레임)** 중 하나인 sub에 값을 설정한다.
• 주로 **사용자 식별자**(ID, username, email)를 저장합니다.

# JWT 표준(Registered Claims)이란?

JWT에는 IETF의 [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519)에서 정의한 몇 가지 **예약된 클레임(Registered Claims)**이 있다.

| **메서드**      | **의미**       | **예제 값**                        |
| ------------ | ------------ | ------------------------------- |
| subject()    | 사용자 식별자 (주체) | “user123”, “admin\@example.com” |
| issuer()     | 토큰 발급자       | “your-app”                      |
| expiration() | 만료 시간        | 2024-12-31T23:59:59Z            |
| issuedAt()   | 발급 시간        | 2024-01-01T00:00:00Z            |
| audience()   | 토큰 대상(수신자)   | “your-client-id”                |

# subject()를 사용하는 이유

### 표준 준수

• JWT는 sub라는 표준 필드에 **주체(Subject)**를 저장하도록 권장
• 인증/인가 시스템에서는 주로 사용자 ID나 사용자명을 subject()에 넣어 식별한다.

### 호환성

• 외부 시스템이나 OAuth 2.0, OpenID Connect와 같은 표준 기반 인증 시스템과의 **호환성**
	• 예: Google OAuth에서 sub은 사용자를 고유하게 식별하는 값

### 가독성 향상

• JWT를 디코딩할 때 **누가 이 토큰의 주체인지** 명확히 나타난다.
• claim("username", value)는 커스텀 값이라 명확성이 떨어질 수 있다.

# 언제 claim()을 사용하는 것이 좋은가?

• claim()은 JWT의 표준 필드에 해당하지 않는 **추가 정보**를 담을 때 사용
	• 예를 들면: 사용자 권한 정보 (role, permissions), 사용자 프로필 ID (profileId), 세션 ID
