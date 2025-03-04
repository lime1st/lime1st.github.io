---
{"dg-publish":true,"permalink":"/illiterate//redis/","tags":["redis"],"noteIcon":"","created":"2025-03-04T16:53:00","updated":"2025-03-04T16:53:51+09:00"}
---

## 레디스의 활용 예:

1. 세션
2. 장바구니
3. 조회수
4. 리더보드
5. 방문자 트래킹
6. **캐싱**
7. 자주 바뀌는 데이터 공유

테이블을 사용하지 않기 때문에 스키마가 없다!

1. 테이터의 크기 증가
2. 비정형 데이터의 증가
3. 일관성 데이터 -> 확장성 유연성이 부족하다 -> NoSQL

## Docker-compose

```yml
services:
  redis-stack:
    image: redis/redis-stack
    container_name: redis-stack-compose
    restart: always
    environment:
      REDIS_ARGS: "--requirepass systempass"
    ports:
      - 6379:6379
      - 8001:8001
```

## Redis-CLI

패키지 apt install redis-server 설치하면 cli도 함께 설치 된다. 터미널에서 아래 명령어 사용해 접속

```
# CLI 접근 명령어 
redis-cli

# 외부접속(비밀번호 X) 
redis-cli -h [접근 서버 IP] -p [레디스 실행 프로세스 포트] 

# 외부접속(비밀번호 O) 
redis-cli -h [접근 서버 IP] -p [레디스 실행 프로세스 포트] -a [비밀번호]
```

## Redis-Insight

app store에서 설치해야 외부 레디스에도 접속할 수 있다.
docker의 redis/redis-stack 버전을 설치해 접속(localhost:8001)하면 로컬 레디스만 접속된다.
직접설치도 가능
```dmz file
https://app.redislabs.com/#/rlec-downloads
```

## Redis Type

key - value 의 형태에서 value의 형태를 다양하게 지정할 수 있다. JAVA의 Map 이라고 생각

명령어가 자료형에 따라 지정되어 있다. 
- Key에 저장된 자료형이 다를 경우 오류가 발생한다.
- MSET 으로 저장한 데이터(List)는 GET으로 읽어올 수 없다(SET/GET은 string).
