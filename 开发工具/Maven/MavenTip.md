## Maven Tip	

### 项目开发中遇到的一些问题

#### 1. DependencyManagement

* DependencyManagement 用于在父项目中定义依赖版本，子项目在引入依赖时无需指定版本
* `DependencyManagement`中定义的只是依赖的声明，并不实现引入，因此子项目需要显式的声明需要用到依赖

#### 举例

```xml
<!-- parent -->
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
                <version>2.5.1</version>
            </dependency>
        </dependencies>
</dependencyManagement>

<!-- 子项目 -->

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### 2. <dependency> 中的 <scope> 的作用

* import 在 <dependencyManagement> 中起作用

> ```xml
> 
> ```
>
> 

> * 使用 `<scope>import</scope>` 解决Maven项目单继承问题
> * 这个标签值只能在dependencyManagement标签下使用！
>   并且仅用于type为"pom"的dependency，其意义为引入该dependency的pom中定义的所有dependency定义。

* runtime 在运行、测试时有效，但是在编译代码时无效
* test在本地会引入，不过打包的时候不会被引入，用于maven test结构中代码的正常执行，但不打入包中，以减小线上部署包的尺寸。
* compile为正常引入模式，表示编译会用它，那么大概率情况，执行也会用它，package会打包进去，至少会把依赖的class打包进去。
* provided的作用，是为了本地编译不出错，但package时不打包进去，但按照字面理解，这个玩意儿是要运行时用的啊，那怎么办呢？
  	那就在运行环境中，必须有这个包。
    	比如tomcat中，会有servlet/jsp api,这种api是在运行时使用的，tomcat中自带；
    	但你项目中编译也会用到，为了本地编译不报错，就要引入，但为了不和线上tomcat发生版本冲突，就要标记为runtime，这样本地编译可用，但打包不引入

3. <dependency> 中的 <optional> 的作用

* false （默认） 代表依赖会被传递，例如 A 引入了 B，那么B中的依赖会被引入A中。可能会产生依赖冲突问题。
* ture 代表依赖不会被传递
