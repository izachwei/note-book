### 创建 Selenoid 容器

```bash
docker run -d --name selenoid -p 8081:4444 -e TZ=America/Los_Angeles -v /var/run/docker.sock:/var/run/docker.sock -v /opt/selenoid/config/:/etc/selenoid/:ro -v /opt/selenoid/video/:/opt/selenoid/video/ -v /opt/selenoid/logs/:/opt/selenoid/logs/ --restart always selenoid:latest -conf /etc/selenoid/browsers.json -limit 20 -video-output-dir /opt/selenoid/video/ -service-startup-timeout 120s -session-attempt-timeout 120s -session-delete-timeout 300s -timeout 1000s -log-output-dir /opt/selenoid/logs/
```

### 创建 Selenoid_UI 容器

```bash
docker run -d --name selenoid-ui --restart always  -p 8082:8080  aerokube/selenoid-ui --selenoid-uri=http://172.16.139.29:8081
```

```
{     //...     "port": "",     "tmpfs": {"/tmp": "size=512m", "/var": "size=128m"},     "path" : "",     "volumes": ["/from:/to", "/another:/one:ro"],     "env" : ["TZ=Europe/Moscow", "ONE_MORE_VARIABLE=itsValue"],     "hosts" : ["one.example.com:192.168.0.1", "two.example.com:192.168.0.2"],     "labels" : {"component": "frontend", "project": "my-project"},     "sysctl" : {"net.ipv4.tcp_timestamps": "2", "kern.maxprocperuid": "1000"},     "shmSize" : 268435456, "mem":"1024m"}
```



```properties
4.9. Selenoid CLI Flags
The following flags are supported by selenoid command:

-capture-driver-logs
    Whether to add driver process logs to Selenoid output
-conf string
    Browsers configuration file (default "config/browsers.json")
-container-network string
    Network to be used for containers (default "default")
-cpu value
    Containers cpu limit as float e.g. 0.2 or 1.0
-disable-docker
    Disable docker support
-disable-privileged
    Whether to disable privileged container mode
-disable-queue
    Disable wait queue
-enable-file-upload
    File upload support
-graceful-period duration
    graceful shutdown period in time.Duration format, e.g. 300s or 500ms (default 5m0s)
-limit int
    Simultaneous container runs (default 5)
-listen string
    Network address to accept connections (default ":4444")
-log-conf string
    Container logging configuration file
-log-output-dir string
    Directory to save session log to
-max-timeout duration
    Maximum valid session idle timeout in time.Duration format (default 1h0m0s)
-mem value
    Containers memory limit e.g. 128m or 1g
-retry-count int
    New session attempts retry count (default 1)
-save-all-logs
    Whether to save all logs without considering capabilities
-service-startup-timeout duration
    Service startup timeout in time.Duration format (default 30s)
-session-attempt-timeout duration
    New session attempt timeout in time.Duration format (default 30s)
-session-delete-timeout duration
    Session delete timeout in time.Duration format (default 30s)
-timeout duration
    Session idle timeout in time.Duration format (default 1m0s)
-version
    Show version and exit
-video-output-dir string
    Directory to save recorded video to (default "video")
-video-recorder-image string
    Image to use as video recorder (default "selenoid/video-recorder:latest-release")
```

