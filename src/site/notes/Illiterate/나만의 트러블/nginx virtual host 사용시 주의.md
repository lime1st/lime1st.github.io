---
{"dg-publish":true,"permalink":"/illiterate//nginx-virtual-host/","tags":["nginx"],"noteIcon":"","updated":true}
---

만일 redirect되어 실행되고 있는 서버가 api를 사용한다면 에러가 발생될 수 있다.
반드시 그런 건 아닐 테지만 다음과 같은 설정 등으로 api 통신 설정도 함께 해주어야 한다.

클라우드 서버에 vault를 설치하고 vault server -dev 실행 시 unseal 상태로 실행되는 데 ui 접속 시 계속 에러가 나서 한참을 헤메었다..

다음의 설정으로 해결할 수 있었다...
~~테스트는 로컬에서 하~~

```
server {
    listen 80;
    server_name vault.example.com;

    location / {
        proxy_pass http://127.0.0.1:8200;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 지원 (Vault UI에서 필요할 수 있음)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
    }

    # CORS 및 기타 헤더 추가 (UI가 API와 정상적으로 통신하도록 설정)
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
    add_header Access-Control-Allow-Headers "Authorization, Content-Type";
}
```