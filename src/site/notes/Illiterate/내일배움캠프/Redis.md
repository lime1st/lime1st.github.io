---
{"dg-publish":true,"permalink":"/illiterate//redis/","tags":["redis"],"noteIcon":"","created":"2025-03-04T16:53:00","updated":"2025-03-05T10:26:51+09:00"}
---

## REmote DIctionary Server REDIS

REDIS 는 메모리에서 동작하는 DB이므로 파일 I/O 작업이 일어나지 않는다 -> 그만큼 빠르다!!
하지만 메모리에 저장되므로 프로세스가 종료되면, 즉 메모리가 비워지는 작업(시스템 종료 등)이 일어나면 데이터가 삭제된다. 즉 영속성이 없다.

#### NoSQL

대표적인 Not Only SQL이다. 스키마와 SQL을 사용하는 관계형 데이터베이스와 달리 데이터를 관리하는 방법이 DB 마다 다르다.

## 레디스의 활용 예:

Session Clustering, Caching[[Illiterate/내일배움캠프/Caching 전략\|Caching 전략]], 리더보드, 방문수 트래킹, 좌표 기반 검색 등

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

#### 타입 별 명령

| type       | command                                                            |
| ---------- | ------------------------------------------------------------------ |
| String     | GET, SET, INCR, DECR, MSET, MGET                                   |
| List       | LPUSH, RPUSH, LPOP, RPOP, LLEN, LRANGE                             |
| Set        | SADD, SREM, SMEMBERS, SISMEMBER, SCARD, SINTER, SUNION, SINTERCARD |
| Hash       | HSET, HGET, HMGET, HGETALL, HKEYS, HLEN                            |
| Sorted Set | ZADD, ZINCREBY, ZRANK, ZRANGE, ZREVRANK, ZREVRANGE                 |
| 공용 명령      | DEL, EXPIRE, EXPIRETIME, FLUSHDB                                   |


---
[[Illiterate/내일배움캠프/RedisTemplate\|RedisTemplate]]