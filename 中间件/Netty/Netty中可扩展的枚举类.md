## Netty 中可扩展的 Enum

### 枚举类无法被继承

* 由于 Java 枚举类定义的特性，枚举类在被编译后生成的 Class , Enum 本质是个继承 java.lang.Enum 类的 **final** class ，所以在设计框架时，枚举类是**难以被扩展**的

### 可扩展的枚举类

* 参考被编译的 Enum ，可以自定义一套可扩展的枚举类
* `AbstractConstant` 
* `Constant` 
*  `ConstantPool` 存放 `'枚举对象'`

#### Tip:

* 也正因为枚举的不可扩展，所以枚举才安全，实例受控
* **可以使用接口模拟可扩展枚举类**

