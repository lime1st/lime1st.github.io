---
{"dg-publish":true,"permalink":"/illiterate//isolation-level/","tags":["transaction","isolation","격리수준"],"noteIcon":"","created":"2025-03-11T05:24:00","updated":"2025-03-12T02:54:59+09:00"}
---

## Isolation 격리성

트랜잭션 격리 수준은 데이터베이스에서 여러 트랜잭션이 동시에 수행될 때, 각 트랜잭션이 서로에게 미치는 영향을 제어하는 방법을 정의한다.

트랜잭션은 ACID(원자성 Atomicity, 일관성 Consistency, 격리성 Isolation, 지속성 Durability)를 보장해야 한다. 그런데 트랜잭션 간 격리성을 완벽히 보장하려면 트랜잭션의 거의 차례대로 실행해야 한다.

문제는 이렇게 차례대로 실행하게 되면 동시성 처리 성능이 매우 나빠진다는 것이다. 이런 문제로 인해 ANSI 표준은 트랜잭션의 격리 수준을 4단계로 나누어 정의했다.

## 트랜잭션 격리 수준 isolation level 과 문제점

| 격리 수준            | DIRTY READ | NON-REPEATABLE READ | PHANTOM READ |
| ---------------- | :--------: | :-----------------: | :----------: |
| READ UNCOMMITTED |     O      |          O          |      O       |
| READ COMMITTED   |            |          O          |      O       |
| REPEATABLE READ  |            |                     |      O       |
| SERIALIZABLE     |            |                     |              |

#### READ UNCOMMITTED

==커밋하지 않은 데이터==를 읽을 수 있다. 예를 들면 트랜잭션 1이 데이터를 수정하고 있는데 커밋하지 않아도 트랜잭션 2가 수정 중인 데이터를 조회할 수 있다. 이것을 `DIRTY READ`라 한다. 트랜잭션 2가 `DIRTY READ`한 데이터를 사용하는 데 트랜잭션 1을 롤백하면 데이터 정합성에 심각한 문제가 발생할 수 있다. `DIRTY READ`를 허용하는 격리수준을 **READ UNCOMMITTED**라 한다. 

#### READ COMMITTED

==커밋한 데이터만== 읽을 수 있다. 그러므로 `DIRTY READ`가 발생하지 않는다. 하지만 `NON-REPEATABLE READ`는 발생할 수 있다. 예를 들어 트랜잭션 1이 회원 A를 조회 중인데 갑자기 트랜잭션 2가 회원 A를 수정하고 커밋하면 트랜잭션 1이 다시 회원 A를 조회했을 때 **수정된 데이터가 조회**된다. 이처럼 반복해서 같은 데이터를 읽을 수 없는 상태를 `NON-REPEATABLE READ`라 한다. `DIRTY READ`는 허용하지 않지만 `NON-REPEATABLE READ`는 허용하는 격리 수준을 **READ COMMITTED**라 한다.

#### REPEATABLE READ

한 번 조회한 데이터를 반복해서 조회해도 ==같은 데이터==가 조회된다. 트랜잭션이 시작된 이후의 데이터 상태를 보장한다. 즉, 같은 트랜잭션 내에서 동일한 쿼리를 실행할 때 항상 같은 결과를 반환한다. 하지만 `PHANTOM READ`는 발생할 수 있다. 예를 들어 트랜잭션 1이 10살 이하의 회원을 조회했는데 트랜잭션 2가 5살 회원을 추가하고 커밋하면 트랜잭션 1이 다시 10살 이하의 회원을 조회했을 때 회원 하나가 추가된 상태로 조회된다. 이처럼 ==반복 조회 시 결과 집합이 달라지는 것==을 `PHANTOM READ`라 한다. `NON-REPEATABLE READ`는 허용하지 않지만 `PHANTOM READ`는 허용하는 격리 수준을 **REPEATALE READ**라 한다.

#### SERIALIZABLE

가장 엄격한 트랜잭션 격리 수준이다. 모든 트랜잭션이 직렬적으로 실행된 것처럼 동작한다. 여기서는 `PHANTOM READ`가 발생하지 않는다. 모든 병행성 문제를 방지하지만, 동시성 처리 성능이 급격히 떨어질 수 있다.

---
출처: [자바 ORM 표준 JPA 프로그래밍](https://product.kyobobook.co.kr/detail/S000000935744)

[[Illiterate/내일배움캠프/Transaction scope persistence context\|Transaction scope persistence context]]