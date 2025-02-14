---
{"dg-publish":true,"permalink":"/illiterate//erd/","tags":["erd"],"noteIcon":"","created":"2025-02-12T23:07:00","updated":"2025-02-14T23:54:40+09:00"}
---

프로젝트가 시작되자마자 알고 있다고 생각했던 것들이 헷갈린다.
팀 원들과 이야기를 나누다가 튜터님께 도움을 받기로 하고 무작정(?) 찾아 갔었던것 같은데 잘한 것 같다!

앞으로 용어의 정의를 더 명확히 인지하고 있어야 겠다.

### 테이블 명세서

- DB 테이블의 컬럼, 제약 조건, 관계 등을 상세히 정리한 문서
- 필드(컬럼), 데이터 타입, 제약 조건, 관계 등을 문서화

##### 예 (Users 테이블)

**테이블 명세서**

|**항목**|**설명**|
|---|---|
|**테이블명**|users|
|**설명**|사용자 정보를 저장하는 테이블|
|**PK**|id (자동 증가)|
|**인덱스**|email (UNIQUE)|

**컬럼 명세**

| **컬럼명**    | **데이터 타입**   | **PK** | **FK** | **NULL 허용** | **기본값**           | **설명**    |
| ---------- | ------------ | ------ | ------ | ----------- | ----------------- | --------- |
| id         | BIGINT       | v      | x      | x           | AUTO_INCREMENT    | 기본 키      |
| name       | VARCHAR(100) | x      | x      | x           | NULL              | 사용자 이름    |
| email      | VARCHAR(255) | x      | x      | x           | NULL              | 이메일 (유니크) |
| password   | VARCHAR(255) | x      | x      | x           | NULL              | 암호화된 비밀번호 |
| created_at | TIMESTAMP    | x      | x      | x           | CURRENT_TIMESTAMP | 생성 시간     |
| updated_at | TIMESTAMP    | x      | x      | v           | NULL              | 업데이트 시간   |

### ERD Entity Relation Diagram

- 데이터베이스 설계를 시각적으로 표현하는 다이어그램
- ERD 구성 요소: Entity, Attribute(PK, FK...), Relationship

##### ERD 예

```
+-----------+       +----------+       +-----------+
| Customer  | 1  ───| Order    |── N  ─| Product   |
+-----------+       +----------+       +-----------+
| id (PK)   |       | id (PK)  |       | id (PK)   |
| name      |       | date     |       | name      |
| email     |       | total    |       | price     |
+-----------+       +----------+       +-----------+
```


### 협업 도구

[ERD 그리기](https://app.diagrams.net/): 오늘 팀원 한 분이 공유해 주어서 함께 ERD 를 작성해 보았다. 공유기능이 있어 서로 맡은 부분을 한 페이지에 작업할 수 있는 기능이 있다는 것을 알게 되었다. ㅎㅎ

[[Illiterate/내일배움캠프/API, 테이블 설계 피드백\|API, 테이블 설계 피드백]]