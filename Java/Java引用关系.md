## Java引用关系

### 强引用  默认

* A a = new A();

### 软引用 

* 在垃圾回收器 GC 时，内存不够用的情况下 会回收此类对象

### 弱引用

* 当 JVM 进行垃圾回收时，**无论内存是否充足**，都会回收被弱引用关联的对象。

### 虚引用

* 最弱的引用，JDK 堆外内存使用了此类引用 `DirectByteBuffer` -> `Cleane`对象 

* ```java
  DirectByteBuffer 
      Cleaner extends PhantomReference
      	使用 虚引用 ReferenceQueue 机制来清除 堆外内存
  ```

* 