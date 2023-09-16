## ByteBuffer

```
PooledByteBufAllocator.buffer ->  AbstractByteBufAllocator.directBuffer 
-> AbstractByteBufAllocator.directBuffer -> PooledByteBufAllocator.newDirectBuffer
-> PoolThreadLocalCache(FastThreadLocal).get() 
-> PoolThreadCache.PoolArena.allocate()
-> PooledUnsafeDirectByteBuf.newInstance(maxCapacity) (工厂模式，享元模式)-> Recycler.get() (threadLocal 线程复用资源，无锁化，保证线程安全) PooledByteBuf
-> PoolArena.allocate (分配内存核心，页内存缓存) 非线程安全，代码中存在同步（资源比较重，无法利用ThreadLocal避免线程安全）
-> PoolThreadCache.allocateTiny()/allocateSmall (小于 arena 中的最小的 bin，那么优先从线程中对应的 tcache 中进行分配，优先从缓存中分配。缓存已分配内存资源ByteBuffer)
```



