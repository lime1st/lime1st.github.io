---
{"dg-publish":true,"permalink":"/illiterate//api/","tags":["api","docker-compose","테이블설계"],"noteIcon":"","created":"2025-02-14T10:12:00","updated":"2025-02-14T10:23:24+09:00"}
---

S.A 제출을 완료하고 담당 튜터님의 피드백을 받았다.

# API 설계

### PUT, PATCH 의 의미!

##### PUT

리소스 전체를 수정한다!

##### PATCH

리소스의 일부를 수정한다.

put, patch의 의미를 명확히 알고 사용한다면 괜찮을 것 같다.

### URL

api url 구성 시 주 도메인을 우선적으로 url에 넣는 것이 의미가 명확해서 좋다.
예를 들면 우리가 현재 개발하는 프로젝트에서 주문을 한다고 하면
	/api/shop/{shop_id}/orders 이렇게 하면 주문이 shop에 종속되어 보인다.
	/api/orders 이렇게 하면 주문을 한다는 의도가 명확해 지므로 의미상 좋은 것 같다. 
추가 정보다 필요하다면 /api/members/address 이런 식으로...우선되는 도메인명을 앞쪽에 배치하자.

### /api/v1 api 버저닝

버저닝을 고려한 api설계는 운영에 어려움이 많이 발생할 수 있다.

# 테이블 설계

정규화, 역정규화, 
반정규화도 필요하다! 데이터 중복을 일부 허용, 성능, 운영상 이점을 위함

# 개발환경

로컬 개발환경을 팀원들간 공유하기 위해 도커를 사용하기로 했다.
docker-compose.yml 파일을 만들어 공유하고 같은 개발환경을 만들어 나가자!

```yml
version: '3.8'

services:
  db:
    image: postgres:16.3
    container_name: postgresql
    restart: always
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: 1234
      POSTGRES_DB: fourseason
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```
