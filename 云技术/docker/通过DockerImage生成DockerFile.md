## 通过 Docker Image 生成 DockerFile

```bash
// a.newegg.org/newegg-docker 可替换
alias dfimage="docker run -v /var/run/docker.sock:/var/run/docker.sock --rm a.newegg.org/newegg-docker/alpine/dfimage" 
dfimage -sV=1.36 7270ef2de0b0
```



