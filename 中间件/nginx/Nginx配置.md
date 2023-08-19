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

## 配置页面降级方案

### 当服务异常时，返回默认页面

```json
server {
    # Other server configurations...

    location / {
        proxy_pass http://backend_server;
        proxy_intercept_errors on;
        error_page 502 503 504 /static/down.html;
    }

    location = /static/down.html {
        # Path to your static "down" page
        root /path/to/static/files;
    }
}

```

### 当服务异常时，返回缓存数据

```json
http {
    # Other http configurations...

    proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m max_size=10g inactive=60m;

    server {
        # Other server configurations...

        location / {
            proxy_pass http://backend_server;
            proxy_cache my_cache;
            proxy_cache_use_stale error timeout invalid_header updating http_500 http_502 http_503 http_504;
        }
    }
}
```

### 当服务异常时，返回临时数据

```json
http {
    # Other http configurations...

    map $upstream_response_time $fallback_response {
        default "The server is currently unavailable. Please try again later.";
    }

    server {
        # Other server configurations...

        location / {
            proxy_pass http://backend_server;
            proxy_intercept_errors on;
            error_page 502 503 504 = @fallback;
        }

        location @fallback {
            return 503 $fallback_response;
        }
    }
}
```

