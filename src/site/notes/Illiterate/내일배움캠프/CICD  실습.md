---
{"dg-publish":true,"permalink":"/illiterate//cicd/","tags":["ci/cd"],"noteIcon":"","created":"2025-02-10 15:27","updated":"2025-02-11 01:19"}
---

ci/cd 강의를 들으면서 첫번째 과제로 만들었던 myselectshop 애플리케이션에 적용해 보았다.
적용하면서 발생한 문제들을 해결해가며 수정한 부분을 적어본다.

### 1. 스크립트 수정

강의를 듣고 실습하는 과정에서 작성한 스크립트를 무작정 적용해보고 에러 나는 부분을 하나씩 수정해 나갔다.

aws에 처음 배포할 때 openjdk의 자바를 설치했기에 distribution: 'temurin' 부분을 수정했다.

### 2. 환경변수 설정

aws의 정책변경으로 rds에 퍼블릭 ip를 적용하지 못하게 되어서(적용하면 과금 발생!) ec2와 연결하여 배포하였다. [[Illiterate/내일배움캠프/AWS EC2 RDS 연결하기\|AWS EC2 RDS 연결하기]]

이 때 테스트와 구분하기 위해 그냥 서버에서 애플리케이션을 실행할 때 커맨드라인 인수를 사용해 db주소와 username, password를 설정했었는데 자동배포를 구성하려니 스크립트가 너무 길어졌다.

그래서 서버에 환경변수로 설정하기로 했다.

```
export SPRING_DATASOURCE_URL="jdbc:mysql://rds endpoint:3306/shop"
```
이런 식으로 환경변수를 설정하고 실행테스트도 완료하고 깃허브액션을 실행 했는데 서버가 실행되지 않았다...

### 3. 환경변수 전역 설정

이리 저리 검색해보니 서버에 직접 접속해서 적용한 환경변수가 깃허브액션에서 사용하는 세션에서 적용되지 않는 다는 것을 알게 되었다.

/etc/environment 에 환경변수를 모든 세션에서 사용할 수 있도록 설정하였다.

이렇게 하니 깃허브 액션에서도 서버의 환경변수를 사용할 수 있어 서버가 잘 실행되었다.

