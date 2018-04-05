---
layout: post
title: Nginx reverse proxy settings for Node.js
categories: yarn
tags: nginx proxy reverse-proxy setting nodejs
---

단순한 리버시 프록시 헤더 설정은 다음과 같이 할 수 있다. (기록용)

```
server {
    listen  8080;
    server_name localhost;

    location / {
        proxy_pass  http://localhost:8888;
        proxy_set_header  Host  $host;
        proxy_set_header  X-Real_IP $remote_addr;
    }
}
```

# 참고문헌

Passing Request Headers: [https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy/)
