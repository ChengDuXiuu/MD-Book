## Java线程synchronized 锁机制是怎样的？

![image-20210620212717822](Java线程锁机制.assets/image-20210620212717822.png)



* 对象头信息如上图，Java锁 synchronized 信息就存放在对象头中

* synchronized 分为方法和代码块

	* 如果synchronized修饰方法则 并没有对应的jvm指令，它是作为修饰符出现的

		![image-20210620213641852](Java线程锁机制.assets/image-20210620213641852.png)

	* 如果synchronized 修饰代码块  则 会产生对应的jvm指令 monitorenter和monitorexit

		![image-20210620213819517](Java线程锁机制.assets/image-20210620213819517.png)

		> :a:<font color=ff00aa size=4>注意，简单来说在JVM中monitorenter和monitorexit字节码依赖于底层的操作系统的Mutex Lock来实现的，但是由于使用Mutex Lock需要将当前线程挂起并从用户态切换到内核态来执行，这种切换的代价是非常昂贵的；如果多次遮掩切换会造成性能的大大浪费，因此在JDK1.6的时候对锁做了大量的优化，出现了现在的</font>  偏向锁、轻量级锁、重量级锁



## 偏向锁、轻量级锁、重量级锁有什么区别？锁机制如何升级？

`引言：`

JDK1.5时，**synchronized** 锁机制是一个==隐式==、==重量级锁==、==悲观锁==，通过系统自身的互斥量来实现，这种方式会导致 用户态到系统态的切换。造成很大的性能浪费，因此JDK1.6时提出了轻量级锁，以及锁升级，都是针对 **synchronized** 而言

> JDK1.6 对 synchronized 优化   偏向锁、轻量级锁、重量级锁 出现是为了解决 synchronized 作为代码块出现时对应的jvm指令monitorenter和monitorexit字节码，由用户态切换为内核态，这种消耗极大。

![image-20210620212316064](Java线程锁机制.assets/image-20210620212316064.png)

1. 当线程走到monitorenter和monitorexit 处，会查看锁对象中锁状态，**线程竞争并不激烈又或者说是线程并没有发生锁争抢** ，这个时候 锁对象会是 `偏向锁`
2. 当线程竞争激烈时，锁对象中锁信息就会变为 `轻量级锁` 即所有线程不停的轮询查看锁是否没有了，期间什么事也不干，导致CPU就处理线程切换了，这个时候如果线程竞争更加激烈则jvm直接交给系统执行`重量级锁`。
3. `轻量级锁利用的就是CAS原理实现`
4. **本身乐观锁就适合冲突不是很激烈的资源上，这样提高了 CAS 的成功率，如果竞争激烈的资源，适合悲观锁；**



## Lock锁机制

> 



## lock和synchronized

* synchronized 是 JVM 底层的实现，Lock 是 JDK 层面的实现；

* synchronized 会自动释放锁，Lock 必须手动释放锁；

* synchronized 只能是非公平锁，Lock 可以设置是公平锁，或者非公平锁，

* Lock 还可以设置为读锁，提高并发度，并且提供了非阻塞式的加锁，tryLock方法，允许设置一个超时时间来加锁；

* synchronized 是不可中断的，Lock 可以设置为可中断或不可中断；

* **建议在资源竞争不激烈的情况下，lock 更适合，因为大量使用到 CAS；**





