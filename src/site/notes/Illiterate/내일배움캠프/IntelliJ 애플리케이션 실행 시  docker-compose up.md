---
{"dg-publish":true,"permalink":"/illiterate//intelli-j-docker-compose-up/","tags":["intellij"],"noteIcon":"","created":"2025-02-15T14:30:00","updated":"2025-03-12T02:48:08+09:00"}
---

프로젝트에 도커를 사용해 팀원들과 로컬 개발환경을 공유하기로 했다. 
애플리케이션을 실행 하기 전에 docker 실행여부를 신경쓰기 않기 위해 애플리케이션이 실행 될 때 docker compose up 을 자동으로 실행하도록 구성해 보자.

## 1. 실행 환경 설정 메뉴
Run / Edit Configurations.. 

![Screenshot 2025-02-15 at 2.29.01 pm.png](/img/user/98.%20Attach/img/Screenshot%202025-02-15%20at%202.29.01%20pm.png)

## 2. 실행환경 추가하기

 +버튼을 누르고 docker / docker-compose 를 선택하고 
 Run 패널에 있는 Compose files 항목에 실행하고자 하는 docker-compose.yml 파일을 선택한다.
 Name: 은 마음대로? 
![Pasted image 20250215151031.png](/img/user/98.%20Attach/img/Pasted%20image%2020250215151031.png)

## 3. 스프링 부트 애플리케이션을 선택

Before launch 패널이 보이면 + 를 눌러 Run Another Application 메뉴를 선택해 2. 에서 설정한 docker-compose Name: 을 선택해준다.
Before launch 패널이 안보이면 Build and run 패널에 있는 Modify options를 누르고 아래로 내려가면 선택할 수 있다.

![Pasted image 20250215151230.png](/img/user/98.%20Attach/img/Pasted%20image%2020250215151230.png)

## 4. 실행하면 된다.

docker compose up을 다시 실행하면 기본적으로 기존 컨테이너를 유지하면서 실행되고, 설정이 바뀌면 **자동으로 변경된 부분만 업데이트되는 방식**