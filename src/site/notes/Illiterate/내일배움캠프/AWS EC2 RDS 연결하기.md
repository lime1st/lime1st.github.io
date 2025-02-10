---
{"dg-publish":true,"permalink":"/illiterate//aws-ec-2-rds/","tags":["aws"],"noteIcon":"","updated":"2025-02-07"}
---

### EC2에서 RDS로 접속 설정하기

우선 EC2와 RDS가 내부 네트워크로 연결이 되어야 한다.

RDS의 인스턴스를 선택한 뒤 작업 > EC2 연결 설정 메뉴에서 프리티어 EC2를 선택해 연결되도록 한다.

### EC2에 접속한 후 RDS에 연결하기 위해 mysql client 를 설치 한다.

```
sudo apt install mysql-client -y
```

설치 후 RDS 인스턴스의 엔드포인트를 복사해 EC2에서 접속 한다.

```
mysql -h [RDS의 엔드포인트] -u [RDS에서 설정한 계정] -p
```
