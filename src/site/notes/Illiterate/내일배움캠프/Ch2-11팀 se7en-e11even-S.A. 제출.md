---
{"dg-publish":true,"permalink":"/illiterate//ch2-11-se7en-e11even-s-a/","noteIcon":"","created":"2025-03-13T12:49:00","updated":"2025-03-13T12:48:56+09:00"}
---

**Ch.2 대규모 AI 시스템 프로젝트 S.A 제출**

팀이름: 11팀 세븐일레븐
팀원: 백승규, 오연주, 임승택, 한석규

노션 페이지 링크: https://www.notion.so/teamsparta/S-A-Starting-Assignments-1b42dc3ef51480c4a7c7fa078e10230d

## API 명세서

API 명세서는 블로그 글로 옮기기에 어려움이 있어 노션 페이지 참조 부탁드립니다.

## 테이블 명세서

###### 사용자 테이블(p_user)

| 제약조건 | 이름     | 필드명           | 타입           | Null 여부 | 설명                                                     |
| ---- | ------ | ------------- | ------------ | ------- | ------------------------------------------------------ |
| PK   | 사용자 ID | user_id       | UUID         | NN      |                                                        |
|      | 닉네임    | username      | VARCHAR(100) | NN      | `최소 4자 이상, 10자 이하이며 알파벳 소문자(a~z), 숫자(0~9)`             |
|      | 비밀번호   | password      | VARCHAR(100) | NN      | `최소 8자 이상, 15자 이하이며 알파벳 대소문자(a~z, A~Z), 숫자(0~9), 특수문자` |
|      | 슬랙 계정  | slack_account | VARCHAR(100) | NN      |                                                        |
|      | 역할     | role          | VARCHAR(100) | NN      |                                                        |
|      | 생성일    | created_at    | TIMESTAMP    | NN      |                                                        |
|      | 생성자    | created_by    | VARCHAR(100) | NN      |                                                        |
|      | 수정일    | updated_at    | TIMESTAMP    |         |                                                        |
|      | 수정자    | updated_by    | VARCHAR(100) |         |                                                        |
|      | 삭제일    | deleted_at    | TIMESTAMP    |         |                                                        |
|      | 삭제자    | deleted_by    | VARCHAR(100) |         |                                                        |
|      |        |               |              |         |                                                        |

###### 슬랙 메시지 테이블(p_sns)

| 제약조건 | 이름           | 필드명        | 타입           | Null 여부 | 설명  |
| ---- | ------------ | ---------- | ------------ | ------- | --- |
| PK   | SNS ID       | sns_id     | UUID         | NN      |     |
| FK   | 수신 받을 사용자 ID | user_id    | UUID         | NN      |     |
|      | 전달 메시지       | message    | TEXT         | NN      |     |
|      | 전달 시간        | time       | TIMESTAMP    | NN      |     |
|      | 생성일          | created_at | TIMESTAMP    | NN      |     |
|      | 생성자          | created_by | VARCHAR(100) | NN      |     |
|      | 수정일          | updated_at | TIMESTAMP    |         |     |
|      | 수정자          | updated_by | VARCHAR(100) |         |     |
|      | 삭제일          | deleted_at | TIMESTAMP    |         |     |
|      | 삭제자          | deleted_by | VARCHAR(100) |         |     |

###### 배송 테이블(p_delivery)

| 제약조건 | 이름           | 필드명                         | 타입           | Null 여부 | 설명  |
| ---- | ------------ | --------------------------- | ------------ | ------- | --- |
| PK   | 배송 ID        | delivery_id                 | UUID         | NN      |     |
| FK   | 주문 ID        | order_id                    | UUID         | NN      |     |
|      | 출발 허브 ID     | departure_hub_id            | UUID         | NN      |     |
|      | 목적지 허브 ID    | destination_hub_id          | UUID         | NN      |     |
|      | 배송지 주소       | delivery_address            | TIMESTAMP    | NN      |     |
|      | 수령인          | receiver                    | VARCHAR(100) | NN      |     |
|      | 수령인 슬랙 ID    | receiver_sns_id             | UUID         | NN      |     |
|      | 업체 배송 담당자 ID | company_delivery_manager_id | UUID         | NN      |     |
|      | 배송상태         | delivery_status             | VARCHAR(100) | NN      |     |
|      | 생성일          | created_at                  | TIMESTAMP    |         |     |
|      | 생성자          | created_by                  | VARCHAR(100) |         |     |
|      | 수정일          | updated_at                  | TIMESTAMP    |         |     |
|      | 수정자          | updated_by                  | VARCHAR(100) |         |     |
|      | 삭제일          | deleted_at                  | TIMESTAMP    |         |     |
|      | 삭제자          | deleted_by                  | VARCHAR(100) |         |     |

###### 배송 경로 테이블(p_delivery_route)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|배송 경로 ID|delivery_route_id|UUID|NN||
|FK|배송 ID|delivery_id|UUID|NN||
|FK|배송 담당자 ID|delivery_person_id|UUID|NN||
||배송 순번|sequence|INT||배송 경로 상 허브의 순번|
||출발 허브 ID|departure_hub_id|UUID|NN||
||도착 허브 ID|arrival_hub_id|UUID|NN||
||예상 거리|expected_distance|TIMESTAMP|NN||
||예상 소요시간|expected_time|VARCHAR(100)|NN||
||실제 거리|actual_distance|UUID|NN||
||실제 소요시간|actual_time|UUID|NN||
||배송상태|delivery_status|VARCHAR(100)|NN||
||생성일|created_at|TIMESTAMP|||
||생성자|created_by|VARCHAR(100)|||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 배송 담당자 테이블(p_delivery_person)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|배송 담당자 ID|delivery_person_id|UUID|NN||
|FK|사용자 ID|user_id|UUID|NN||
||슬랙 ID|sns_id|UUID|NN||
||소속 허브 ID|hub_id|UUID|NN||
||배송 담당자 타입|type|VARCHAR(100)|NN|업체 배송 담당자,  <br>허브 배송 담당자|
||배송 순번|sequence|INT||배송 경로 상 허브의 순번|
||생성일|created_at|TIMESTAMP|||
||생성자|created_by|VARCHAR(100)|||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 허브 테이블(p_hub)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|허브 ID|hub_id|UUID|NN||
||허브 이름|name|VARCHAR(100)|NN||
||허브 주소|address|VARCHAR(100)|NN||
||위도|latitude|DOUBLE|NN||
||경도|longitude|DOUBLE|NN||
||생성일|created_at|TIMESTAMP|NN||
||생성자|created_by|VARCHAR(100)|NN||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 허브 간 이동경로 테이블(p_hub_log)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|허브 로그 ID|hub_log_id|UUID|NN||
|FK|출발 허브 ID|origin_hub_id|UUID|NN|`최소 4자 이상, 10자 이하이며 알파벳 소문자(a~z), 숫자(0~9)`|
|FK|도착 허브 ID|destination_hub_id|UUID|NN|`최소 8자 이상, 15자 이하이며 알파벳 대소문자(a~z, A~Z), 숫자(0~9), 특수문자`|
||소요 시간|delivery_time|VARCHAR(100)|||
||배달 거리|distance|VARCHAR(100)|||
||생성일|created_at|TIMESTAMP|NN||
||생성자|created_by|VARCHAR(100)|NN||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 업체 테이블(p_company)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|업체 ID|company_id|UUID|NN||
|FK|사용자 ID|user_id|UUID|||
||업체 관리 허브 ID|hub_id|UUID|NN||
||업체 타입|type|VARCHAR(100)|NN|생산 업체,  <br>수령 업체|
||업체 주소|address|VARCHAR(100)|NN||
||업체 이름|name|VARCHAR(100)|NN||
||생성일|created_at|TIMESTAMP|NN||
||생성자|created_by|VARCHAR(100)|NN||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 상품 테이블(p_product)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|상품 ID|product_id|UUID|NN||
|FK|업체 ID|company_id|UUID|NN||
||상품 관리 허브 ID|hub_id|UUID|NN||
||상품명|product_name|VARCHAR(100)|NN||
||재고 수량|stock_quantity|INT|NN||
||생성일|created_at|TIMESTAMP|NN||
||생성자|created_by|VARCHAR(100)|NN||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 주문 테이블(p_order)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|주문 ID|order_id|UUID|NN||
||공급 업체 ID|supply_id|UUID|NN||
||수령 업체 ID|receiver_id|UUID|NN||
|FK|배송 ID|delivery_id|UUID|||
||요청사항|request|VARCHAR(100)||`납품기한 일자 및 시간 등`|
||생성일|created_at|TIMESTAMP|NN||
||생성자|created_by|VARCHAR(100)|NN||
||수정일|updated_at|TIMESTAMP|||
||수정자|updated_by|VARCHAR(100)|||
||삭제일|deleted_at|TIMESTAMP|||
||삭제자|deleted_by|VARCHAR(100)|||

###### 주문 상품 테이블(p_order_product)

|제약조건|이름|필드명|타입|Null 여부|설명|
|---|---|---|---|---|---|
|PK|주문 상품 ID|order_product_id|UUID|NN|`상품 주문 연관관계 테이블`|
|FK|주문 ID|order_id|UUID|NN||
|FK|상품 ID|product_id|UUID|NN||
||상품 수량|quantity|INT|NN||
||생성일|created_at|TIMESTAMP(100)|NN||
||생성자|created_by|VARCHAR|NN||
||수정일|updated_at|TIMESTAMP(100)|||
||수정자|updated_by|VARCHAR|||
||삭제일|deleted_at|TIMESTAMP(100)|||
||삭제자|deleted_by|VARCHAR|||

## ERD 명세서

![erd.png](/img/user/98.%20Attach/erd.png)

## 인프라 설계서

![infra.png](/img/user/98.%20Attach/infra.png)