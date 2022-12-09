## JavaAgent 技术

JavaAgent 又称 Java 探针，是在 JDK5 引进的一种动态修改字节码的技术。它在 JVM 在加载字节码之前，获取字节码信息 通过字节码转换器修改字节码来实现一些额外的功能。JavaAgent本质可以理解成一个Jar包插件，使用 JVMTI（JVM Tool Interface）完成加载，利用 JVM 提供的 `Instrumentatiion` 接口来实现字节码加载前、加载后的修改。-javaagent 参数来指定一个 agent jar包，实现启动jvm时加载 agent。利用这一特性可以实现虚拟机级别的 AOP ，实时监控、分析虚拟机，干预虚拟机的执行。

### Java 能做什么？

* 在 JVM 加载字节码之前对字节码修改
* 在 JVM 加载字节码之后对字节码修改
* 获取当前 JVM 加载的所有类信息
* 获取 JVM 初始化的类信息
* 修改 native 方法
* 获取某个对象的大小
* 将某个jar加入到bootstrapclasspath里作为高优先级被bootstrapClassloader加载
* 将某个jar加入到classpath里供AppClassloard去加载

### 实现方式

#### 启动 JVM 时加载 Agent(premain)

##### 定义 Agent

```java
public class MyAgent {
    public static void premain(String[] args, Instrumentation instrumentation) {
        System.out.println("JVM starting, load agent");
    }
}
```

##### 定义 `MANIFEST.MF`，放在 resource/ META-INF/

<img src="../imgs/manifest.png" />

```properties
Manifest-Version: 1.0
Premain-Class: org.example.agent.MyAgent
Agent-Class: org.example.agent.MyAgent
Can-Redefine-Classes: true
Can-Retransform-Classes: true
Can-Set-Native-Method-Prefix: true
```

##### 修改 POM 文件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-assembly-plugin</artifactId>
            <configuration>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <archive>
                    <manifestFile>src/main/resources/META-INF/MANIFEST.MF</manifestFile>
                </archive>
            </configuration>
        </plugin>
    </plugins>
</build>
```

##### 打包 mvn assembly:assembly 进行打包，会生成2个jar包，my-agent-1.0-SNAPSHOT.jar中不带这个项目所依赖的jar包，my-agent-1.0-SNAPSHOT-jar-with-dependencies.jar中包含这个项目所依赖的jar包，后面需要使用my-agent-1.0-SNAPSHOT-jar-with-dependencies.jar这个jar

##### 项目工程引入自定义 agent ，在项目启动时 添加 vm 参数，-javaagent={agentpath}/myagent.jar

### JVM 运行时加载 agent（attachemain）

* 该方法就不是使用  -javaagent 参数引入，需要使用 virtualMachine 下的 attche 方法引入，该类不是 jdk 官方提供，又sun公司提供，需要引入依赖

```java
public class Attacher {

    public static void main(String[] args)
        throws IOException, AttachNotSupportedException, AgentLoadException, AgentInitializationException {
        // 指定进程号
        VirtualMachine vm = VirtualMachine.attach(args[0]);
        // 指定agent包位置和要传入的参数
        vm.loadAgent(
            "/Users/fisher/Documents/code/gitee/myAgent/target/my-agent-1.0-SNAPSHOT-jar-with-dependencies.jar",
            "Hello JVM Attach");
        vm.detach();
    }
}
```

### 打包 POM

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>2.5.5</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>org.example.attach.Attacher</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

利用 jps 查看进程号，选择要加载agent的进程号，启动 agent 传入 进程号，完成 agent 的载入



