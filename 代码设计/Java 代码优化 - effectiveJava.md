## Java 代码优化 - effectiveJava

《Effective-Java》这本书读了有两三遍，随着工作经验增加，每次读都有新的体会，都会或多或少的遇到书本案例的坑。现记录一些在实际项目中遇到的问题。

#### String.matches

避免使用 `String.matches()` 方法进行正则匹配，**但它不适合在性能关键的情况下重复使用。** 问题在于，它在内部为正则表达式创建了一个 Pattern 实例，并且只使用一次。

优化：

显示定义 Pattern 作为静态变量缓存起来，避免重复创建

```
private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
ROMAN.matcher(s).matches();
```

#### 常见的内存泄漏场景

1. 缓存
2. 自己管理内存（数组）
3. 监听器、回调

#### public静态Final字段 要么基础数据类型，要么引用不可变对象

final 引用可变对象具有非 final 字段的所有缺点，虽然引用不可变，但是对象本身的内部数据是可以修改的。

项目案例：

效能平台上定义的 `LaunchInfo` 工厂，当前环境第一次访问没有被创建会返回一个默认空类型保证页面初始化时展示正常。该 `EmptyLaunchInfo` 内部数据可以修改。

其他同事在使用该包时不知道其中的逻辑，导致该静态变量被 A 请求修改使用后，B请求拿到是修改后的数据，出现了bug。

修正：将`EmptyLaunchInfo` 定位不可变，将更新方法修改为抛出异常

```java

public class LaunchInfoFactory {
    public static final EmptyLaunchInfo EMPTY_LAUNCH_INFO = new EmptyLaunchInfo();
    class EmptyLaunchInfo extends AbstractLaunchInfo{

    }
}
```





