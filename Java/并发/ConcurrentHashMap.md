## ConcurrentHashMap

ConcurrentHashMap 的一些方法是线程安全的，一些不是。其中线程安全的包括：get、put、remove、containsKey。线程不安全的包括：size、clear、isEmpty。

其中 get 方法虽然是线程安全的，但是方法中并没有加锁。底层通过 volatile 保证读取的时的可见性（node.val）。Node 若是红黑树，则会加上读写锁进行查询。
