---
{"dg-publish":true,"permalink":"/illiterate//naming-convention/","tags":["convention","staticFactoryMethod"],"noteIcon":"","created":"2025-02-16T15:49:00","updated":"2025-02-16T15:58:48+09:00"}
---

프로젝트 팀원 분이 공유해 주신 내용에서 내가 당장 외우고 있으면 좋을 내용만 정리해보았다.

# 네이밍 종류

### from:
하나의 매개 변수를 받아서 객체를 생성한다.

### of:
여러 개의 매개 변수를 받아서 객체를 생성한다.

### getInstance | instance:
인스턴스를 생성, 이전에 반환했던 것과 같을 수 있다.

### newInstance | create:
항상 새로운 인스턴스를 생성한다.

### get\[Order Type]:
다른 타입의 인스턴스를 생성, 이전에 반환했던 것과 같을 수 있다.

### new\[Order Type]:
항상 다른 타입의 새로운 인스턴스를 생성한다.

# 실제 예제

### Optional의 of()

### List의 of()

### Integer의 valueOf()

입력값이 -128 ~ 127 범위의 정수라면 캐싱을 이용해 리턴한다.

# 정적 팩토리 메서드의 문제점

### private 생성자일 경우 상속 불가능
정적 팩토리 메서드로 클래스를 설계하면 생성자를 private 접근 제어자로 설정하게 되므로 상속을 이용한 확장이 불가능하다.

---
##### 출처: https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90