# Nginx 配置

## 配置重试机制

```
proxy_next_upstream error timeout invalid_header http_500 http_503 http_404;
```

## 代理服务，负载均衡 ，监控检测

```json
http {
      upstream onmpw {
          server 192.168.144.128;
          server 192.168.144.132 max_fails=3 fail_timeout=30s;
          server 192.168.144.131 max_fails=2;
      }
      server {
          listen 80;
          location / {
              proxy_pass http://onmpw;
  			  health_check interval=10 fails=3 passes=2; /* 商用版才支持主动监控检测 */
          }
      }
  }
```

