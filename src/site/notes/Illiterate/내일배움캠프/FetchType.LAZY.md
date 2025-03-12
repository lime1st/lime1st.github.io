---
{"dg-publish":true,"permalink":"/illiterate//fetch-type-lazy/","tags":["jpa","lazy","proxy"],"noteIcon":"","created":"2025-03-12T23:47:00","updated":"2025-03-12T23:48:08+09:00"}
---

## 프록시 객체

프록시 객체는 주로 연관된 엔티티를 지연 로딩할 때 사용한다.
**지연로딩**은 연관된 엔티티를 실제 사용할 때 조회한다.

다음과 같은 @ManyToOne 연관관계에서 지연로딩을 사용한다면 One 측의 객체(team)는 실제 사용되기 전까지는 프록시 객체다.

```jav
@Entity
public class Member {
	//...
	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "TEAM_ID")
	private Team team;
	//...
}

// 지연 로딩 실행 코드
Member member = em.find(Member.class, "member1");
Team team = member.getTeam(); // 객체 그래프 탐색
team.getName(); // 팀 객체 실제 사용
```

Team team = member.getTeam() 에서 반환된 팀 객체는 ==프록시 객체==다. 지연로딩에서는 연관된 엔티티를 프록시로 조회한다. 이 프록시 객체는 다음 행에서 실제 사용되기 전까지는 데이터 로딩을 미룬다. 그래서 지연 로딩이라 한다.

이처럼 실제 데이터가 필요한 순간이 되어서야 데이터베이스를 조회해서 프록시 객체를 초기화한다.


> [!NOTE] 참고
> 조회 대상이 영속성 컨텍스트에 이미 있으면 프록시 객체를 사용할 이유가 없다. 따라서 프록시 객체가 아닌 실제 객체를 사용한다. 예를 들어 team1 엔티티가 영속성 컨텍스트에 이미 로딩되어 있으면 프록시가 아닌 실제 team1 엔티티를 사용한다.

## 프록시와 컬렉션 래퍼

하이버네이트는 엔티티를 영속 상태로 만들 때 엔티티에 컬렉션이 있으면 컬렉션을 추적하고 관리할 목적으로 원본 컬렉션을 하이버네이트가 제공하는 내장 컬렉션으로 변경하는 데 이것을 컬렉션 래퍼라 한다.

엔티티를 지연 로딩하면 프록시 객체를 사용해서 지연 로딩을 수행하지만 주문 내역 같은 컬렉션은 컬렉션 래퍼가 지연 로디을 처리해준다. 컬렉션 래퍼도 컬렉션에 대한 프록시 역할을 한다.

> 참고로 member.getOrders() 처럼 컬렉션을 호출해도 컬렉션은 초기화되지 않는다. 컬렉션은 member.getOrders().get(0) 처럼 컬렉션에서 실제 데이터를 조회할 때 데이터베이스를 조회해서 초기화한다.

---
출처: [자바 ORM 표준 JPA 프로그래밍](https://product.kyobobook.co.kr/detail/S000000935744)

[[Illiterate/내일배움캠프/Transaction scope persistence context\|Transaction scope persistence context]]