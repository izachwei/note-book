
## Synchronized 

### 1. 概念：
* 互斥锁，Java内置锁（隐式锁） 非公平 不可中断 
### 2. 应用：
* 作用方法：同步方法（this）
* 作用代码块：同步代码块（object）
* 作用静态方法：同步方法（锁的类对象）
### 3. 原理：
* 锁的粒度是对象
* 锁膨胀
> * 无锁->偏向锁->轻量锁->重量级锁 
> * 锁升级不可逆
> * 锁的标志 记录再 对象 markword 
* 锁消除
> * 逃逸分析： -XX:DoEscapeAnalysis 
> * 标量替换
> * 对象可以再栈上分配
* 锁粗化
> * 避免频繁枷锁
* 自适应自旋锁
* 偏向锁
* 轻量级锁
* 锁升级	
