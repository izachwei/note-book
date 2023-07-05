### 记录一些开发中常用的 docker 命令

#### 清除没用使用的数据，包括镜像数据、已经停止的容器

`docker system prune -a`

### 查询日志

` docker logs --since="2021-10-07T23:35:00Z" --until="2021-10-07T23:36:00Z"`

#### 查询最近日志（指定多少行）

`docker logs -f -t --tail=100 xxx`
