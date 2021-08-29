* jedis

	jedis存在线程安全问题，不得直接使用连接进行操作，最好使用线程池。

	最优解是全局使用一个jedis线程池，

* lettuce

	内部使用netty保证线程安全

* redision



## jedis客户端

