## 在 Docker 容器不会使用宿主机的代理配置，需要手动设置

Docker 容器里的进程默认情况下不会共享宿主机的代理配置，因为 Docker 容器是独立的运行环境，它有自己的网络栈和 IP 地址。容器中的进程和宿主机上的进程是相互隔离的。

使用 Docker run 命令时指定代理参数。你可以在运行容器时使用 -e 选项来设置 http_proxy 和 https_proxy 环境变量。

`docker run -e http_proxy=http://myproxy:8080 -e https_proxy=http://myproxy:8080 myimage`

这将在容器内部设置 HTTP 和 HTTPS 代理，以便容器内部的进程可以使用代理访问网络。

在 Dockerfile 中设置代理。你可以在 Dockerfile 中使用 ENV 命令来设置环境变量。

```
ENV http_proxy http://myproxy:8080
ENV https_proxy http://myproxy:8080
```



