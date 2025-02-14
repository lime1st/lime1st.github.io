---
{"dg-publish":true,"permalink":"/illiterate//intelli-j/","tags":["intellij","docker-compose"],"noteIcon":"","created":"2025-02-15T00:58:00","updated":"2025-02-15T01:24:01+09:00"}
---

프로젝트에 docker를 사용해 db 설정을 간편하게 하기 위해 docker-compose 파일을 만들었다.
애플리케이션 실행 시마다 docker-compose up, down을 하기 귀찮아 애플리케이션 실행 시 자동으로 작업이 되도록 방법을 찾아보았다. 
당연하게(?)도 intellij에 간단한 설정 방법이 있었다.

###### 1. Run 메뉴의 Edit Configurations를 클릭

![Screenshot 2025-02-15 at 12.34.45 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.34.45%20am.png)

###### 2. + 버튼을 누르고 Docker Compose를 클릭

![Screenshot 2025-02-15 at 12.35.30 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.35.30%20am.png)

###### 3. Compose Files 항목에서 폴더 그림의 아이콘을 선택
나오는 선택창에서 실행할 docker-compose.yml 를 추가, Name에는 적절한 이름을 입력해 준다.

![Screenshot 2025-02-15 at 12.36.15 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.36.15%20am.png)

###### 4. 다시 왼쪽 항목에서 실행할 어플리케이션을 선택
Build and Run 항목에 보이는 Modify options를 눌러주고 Add before launch task를 선택해 준다.

![Screenshot 2025-02-15 at 12.36.45 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.36.45%20am.png)
![Screenshot 2025-02-15 at 12.36.57 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.36.57%20am.png)

###### 5. Add New Task 메뉴가 나오면 Run Another Configuration을 선택
3 에서 만든 작업이름을 선택해 준다. 

![Screenshot 2025-02-15 at 12.37.07 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.37.07%20am.png)

###### 6. 5를 완료하고 나면 Befoe launch 항목이 생긴다.
 \+ 버튼을 누르고 Run External tool 을 선택해 작업을 추가한다.
 
![Screenshot 2025-02-15 at 12.37.38 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.37.38%20am.png)

###### 7. External Tools 추가
\+ 를 누르면 작업을 생성할 수 있는데 Name 항목에 적절한 이름을 적어주고
Program: 항목에는 docker-compose, Arguments: 항목에는 down 을 적은 후 OK를 눌러준다.

![Screenshot 2025-02-15 at 12.37.51 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.37.51%20am.png)
![Screenshot 2025-02-15 at 12.38.24 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.38.24%20am.png)

###### 8. 작업 추가 완료 화면

![Screenshot 2025-02-15 at 12.38.39 am.png](/img/user/98.%20Attach/Screenshot%202025-02-15%20at%2012.38.39%20am.png)