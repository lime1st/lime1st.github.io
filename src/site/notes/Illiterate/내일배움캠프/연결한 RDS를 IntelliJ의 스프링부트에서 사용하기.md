---
{"dg-publish":true,"permalink":"/illiterate//rds-intelli-j/","tags":["intellij","aws"],"noteIcon":"","created":"2025-02-17T00:47:00","updated":"2025-02-17T14:43:45+09:00"}
---

AWS EC2에 퍼블릭 IP를 사용 중이라면 RDS에 ip 사용 시 과금이 발생한다. EC2와 RDS를 연결([[Illiterate/내일배움캠프/AWS EC2 RDS 연결하기\|AWS EC2 RDS 연결하기]])한 후 intelliJ의 스프링부트 개발활경에 사용하려면 SSH/Tunnel을 활용할 수 있다.

 
> [!info] 
> IntelliJ Ultimate Edition 사용

### 1. View > Tool Windows > Database 를 누르면 나오는 우측 창에서 + 를 눌러 데이터 소스를 추가한다.

Data Source 에는 RDS에서 사용하는 DB를 선택한다.

![Pasted image 20250217005157.png](/img/user/98.%20Attach/img/Pasted%20image%2020250217005157.png)

### 2. SSH/SSL 탭 선택

Use SSH tunnel 을 체크하고 ...을 눌러 ssh 연결 설정을 추가한다.
Local port: 애플리케이션에서 연결 설정에 사용할 포트 번호

![Pasted image 20250217010104.png](/img/user/98.%20Attach/img/Pasted%20image%2020250217010104.png)

### 3. SSH Configurations 입력

SSH Configurations 창에서 + 눌러 설정을 추가하고 필요한 항목을 입력해 준다.

- Host: EC2 퍼블릭 IP 혹은 ssh 연결 가능한 엔드포인트
- Username: EC2 ssh 접속시 사용하는 username
- Authentication type: 접속 방법 설정 (key pair 는 선택 후 파일을 지정해 준다.)

Test Connection 을 클릭해 연결이 잘 되는지 확인한다.

![Pasted image 20250217010312.png](/img/user/98.%20Attach/img/Pasted%20image%2020250217010312.png)

### 4. General 탭 선택

- Host: RDS 엔드포인트
- User/Password : DB user와 password를 입력한다.
- Database: RDS에서 생성한 database 이름 입력

Test Connection 으로 RDS의 DB 접속확인(네트워크 환경에 따라 시간이 좀 걸린다...)

![Pasted image 20250217010900.png](/img/user/98.%20Attach/img/Pasted%20image%2020250217010900.png)

### 5. application.properties 파일 설정

```properties
spring.datasource.url=jdbc:mysql://localhost:[2번에서 지정한 포트 번호]/[db이름]
spring.datasource.name=
spring.datasource.password=
```