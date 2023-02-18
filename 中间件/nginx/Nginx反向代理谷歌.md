# Nginx反向代理谷歌

**nginx配置**

```
server {
        listen  80;
        server_name     igoogle.com;
        resolver        8.8.8.8;
        location / {
                root html;
                index index.html index.htm;
                proxy_pass https://www.google.com;
                proxy_connect_timeout 120;
                proxy_read_timeout 600;
                proxy_send_timeout 600;
                send_timeout 600;
                proxy_redirect    off;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

}
```

