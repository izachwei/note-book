## 常用的shell命令

### `if [ $? ``-eq` `0 ]` 一个命令执行后的退出状态

用于判断上一次命令是否执行成功

```
$0: 　　shell或shell脚本的名字
$*:　　 以一对双引号给出参数列表
$@:　 将各个参数分别加双引号返回
$#: 参数的个数
$_:　　代表上一个命令的最后一个参数
$$:　　代表所在命令的PID
$!:　　 代表最后执行的后台命令的PID
$?:　　代表上一个命令执行后的退出状态
```

### 查找 端口号占用的进程

```
netstat -tlnp | grep {port}
```

### 通过 curl 查看网站连接时间

```
curl -L -w "time_namelookup: %{time_namelookup}
time_connect: %{time_connect}
time_appconnect: %{time_appconnect}
time_pretransfer: %{time_pretransfer}
time_redirect: %{time_redirect}
time_starttransfer: %{time_starttransfer}
time_total: %{time_total}
" https://example.com/
```


### 给文件夹及其子文件夹设置权限
```
chmod -R 777 /xxx/
```

