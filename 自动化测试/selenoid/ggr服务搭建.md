## 创建一个 GGR 容器实例

```
docker run -d --name ggr -p 8088:4444 -e TZ=America/Los_Angeles -v /etc/grid-router/:/etc/grid-router/akamai/:ro aerokube/ggr:1.5.4
```

## 如何更新 GGR 配置文件？

如上面命令文件目录的影射，配置文件的根目录：/etc/grid-router/

### 配置文件所在目录地址

hubLink: http://test:123456@10.1.49.12:8083/wd/hub

**/etc/grid-router/quota/test.xml**

文件名 test，便是用户名 。

**/etc/grid-router/users.htpasswd**

用于指定用户名-密码。

### 配置文件各个节点说明

**<browser> 对应浏览器的信息，新增浏览器则新增该节点。**

**<version> 对应浏览器的版本，新增浏览器版本则新增该节点。**

**<host>每个 Host 节点 name 指定 selenoid IP 地址，port 指定 selenoid 服务端口，count 指定轮询权重。**

具体文件内容：

```
<qa:browsers xmlns:qa="urn:config.gridrouter.qatools.ru"> <browser name="chrome" defaultVersion="latest">    <version number="latest">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>     <version number="98.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>    <version number="90.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>    <version number="83.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>    <version number="80.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>    <version number="70.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version>    <version number="65.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version> </browser> <browser name="MicrosoftEdge" defaultVersion="108.0">    <version number="108.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version> </browser> <browser name="firefox" defaultVersion="107.0">    <version number="107.0">        <region name="1">                        <host name="172.16.139.28" port="8081" count="1"/>                        <host name="172.16.139.29" port="8081" count="1"/>                        <host name="172.16.139.26" port="8081" count="1"/>                        <host name="172.16.139.27" port="8081" count="1"/>                        <host name="172.16.139.25" port="8081" count="1"/>                        <host name="172.16.139.37" port="8081" count="1"/>        </region>    </version> </browser> </qa:browsers> 
```

### GGR 配置如何不停机更新？

在平常新增浏览器，或者在某个 selenoid 新增浏览器版本，则需要更新配置文件新增 <version>节点，这种操作相对来说是频繁的。如果是冷更新配置，修改后配置文件重启 GGR 则会影响线上正在运行的 Job。

```
// 修改配置文件 vim /etc/grid-router/quota/test.xml // 发送更新配置指令（官方提供的方法） docker kill -s HUP ggr
```
