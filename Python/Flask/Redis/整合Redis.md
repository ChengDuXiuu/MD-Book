# 整合Redis

1. 安装redis-py

	```bash
	pip3 install redis
	```

2. 连接池连接

	```python
	import redis
	
	redis_pool = redis.ConnectionPool(host='127.0.0.1', port= 6379, password= 'your pw', db= 0)
	redis_conn = redis.Redis(connection_pool= redis_pool)
	```

	

## 基础使用

```python
# RedisPool.py
import redis

try:
    pool = redis.ConnectionPool(
        host='192.168.31.200',
        port=6379,
        db=0,
        decode_responses=True,
        charset='UTF-8',
        encoding='UTF-8'
    )
except Exception as e:
    print(e)
```

```python
#RedisOpt.py
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import redis
from RedisPool import pool


class RedisOpt(object):

    def __init__(self, name):
        self.db = redis.Redis(connection_pool=pool)
        self.key = name

    ###################################################      字符串方法       ###################################################
    # 加入缓存,存在会替换
    def addStr(self, values):  # value可以为复杂的json
        return self.db.set(self.key, values)

    # 不存在则加入,否则不变
    def addStrNX(self, values):  # value可以为复杂的json
        return self.db.setnx(self.key, values)

    # 加入缓存,存在会替换,并加入过期时间
    def addStrEX(self, time, values):  # value可以为复杂的json
        return self.db.setex(self.key, time, values)

    # 获取缓存
    def getStr(self):
        return self.db.get(self.key)

    # 对value增值+count
    def incrNum(self, values=1):
        return self.db.incr(self.key, values)

    # 对value减值-count
    def decrNum(self, values=1):
        return self.db.decr(self.key, values)

    ###################################################      列表方法       ###################################################
    # 返回队列里面list元素的长度
    def listLen(self):
        return self.db.llen(self.key)

    # 添加新元素到队列的最右方
    def add2right(self, *values):
        self.db.rpush(self.key, *values)  # rpush 如何没有列表会创建 rpushx不创建

    # 返回并删除队列里的第一个元素,如果队列为空返回的None
    def getAndPopFirst(self):
        item = self.db.lpop(self.key)
        return item

    # 获取列表对应索引的值
    def getItemIndex(self, index):
        indexValue = self.db.lindex(self.key, index)
        return indexValue

    # 获取列表所有元素
    def getAllListItem(self):  # 获取list所有元素 无key会返回[]
        allItemList = self.db.lrange(self.key, 0, -1)
        return allItemList

    # 获取列表start-end的元素,切头切尾
    def getListItem(self, start, end):
        itemList = self.db.lrange(self.key, start, end)
        return itemList

    # 删除列表中value
    def delValue(self, value):
        self.db.lrem(self.key, 1, value)

    # 删除列表中指定数量的value
    def delMoreValue(self, count, value):  # count为0表示删除所有值为value的元素
        self.db.lrem(self.key, count, value)

    # 获取并只保留start-end的元素,切头切尾
    def saveStart2end(self, start, end):
        start2endList = self.db.ltrim(self.key, start, end)
        return start2endList

    # 批量获取删除
    def batchGetAndPop(self, n):  # n>=1, 返回格式[['value'], True]
        p = self.db.pipeline()
        p.lrange(self.key, 0, n - 1)
        p.ltrim(self.key, n, -1)
        data = p.execute()
        return data

    ###################################################      集合方法       ###################################################
    # 加入集合中
    def add2set(self, *values):
        self.db.sadd(self.key, *values)

    # 查看元素是否存在集合中
    def memberExist(self, member):
        check = self.db.sismember(self.key, member)
        return check

    ###################################################      散列方法       ###################################################
    # 添加
    def set2hash(self, key, value):
        self.db.hset(self.key, key, value)

    # 不存在才添加
    def set2hashNX(self, key, value):
        self.db.hsetnx(self.key, key, value)

    # 批量添加
    def batchSet2hash(self, mapping):
        self.db.hmset(self.key, mapping)

    # 获取key对应的value
    def getValue(self, key):
        value = self.db.hget(self.key, key)  # key不存在会返回None
        return value

    # 批量获取多个key对应的value
    def batchGetValue(self, keyList):
        value = self.db.hmget(self.key, keyList)  # key不存在会返回None ['a', 's', None, None]
        return value

    # 删除key-value
    def delKeyValue(self, *keys):
        self.db.hdel(self.key, *keys)

    # 判断散列中是否存在
    def keyExist(self, key):
        check = self.db.hexists(self.key, key)
        return check

    # 获取所有key
    def getAllKey(self):
        check = self.db.hkeys(self.key)
        return check

    # 获取所有value
    def getAllValue(self):
        check = self.db.hvals(self.key)
        return check

    # 获取所有key-value
    def getAllKeyValue(self):
        check = self.db.hgetall(self.key)
        return check

    # key对应的value增长amount
    def increaseValue(self, key, amount):  # 针对值为数量使用 ,key不存在也可以直接加
        check = self.db.hincrby(self.key, key, amount)
        return check

    # 获取散列长度
    def getDicLen(self):
        check = self.db.hlen(self.key)
        return check

    ###################################################      通用方法       ###################################################
    # 查看过期时间
    def checkExpireTime(self):
        item = self.db.ttl(self.key)
        return item

    # 设置过期时间
    def setExpireTime(self, time):
        item = self.db.expire(self.key, time)
        return item
```



`使用`

```python
from RedisOpt import RedisOpt

if __name__ == "__main__":
    redisOpt = RedisOpt('name')
try:
    redisOpt.addStr("王五")

    print('Redis key:%s. value:%s' % (redisOpt.key, redisOpt.getStr()))

except Exception as e:
    print(e)
finally:
    # 关闭连接
    del redisOpt
```



## 批量处理-事务

```python
from RedisPool import pool
import redis

con = redis.Redis(
    connection_pool=pool
)

try:
    pipline = con.pipeline()
    pipline.watch("pipline")
    pipline.multi()
    pipline.hset('pipline', 'name', '测试Redis事务-批量处理')
    pipline.hset('pipline', 'age', 28)
    pipline.hset('pipline', 'sex', '男')
    pipline.execute()

except Exception as e:
    print(e)
finally:
    if 'pipline' in dir():
        pipline.reset()
    del pipline

```



## 存入对象报错

> TypeError: Object of type bytes is not JSON serializable
>
> TypeError: 'dict' object is not callable

### 根因

```python
pool = redis.ConnectionPool(
  host='192.168.31.200',
  port=6379,
  db=0,
  decode_responses=True, //使用utf-8编码和转码
)
```

> 在连接Redis的时候，默认`decode_responses=False`，则redis中存储的键值对均为bytes类型，如果我们需要使用字符串时需要**encode(),将str转换为bytes或者decode将查询出的bytes值转换为str**，因此如果使用redis是用来存储字符串等键值对，推荐使用`decode_responses=True`

> 当我们存储对象或者dict等时，pickle会将对象dumps成bytes类型的字符串，并且该字符串无法使用utf-8进行decode，且设置`decode_responses=True`后会导致解码使用utf-8对bytes类型字符串解码进而抛出异常。因此如果使用redis**会涉及对象，字典，列表等的存储**，推荐使用`decode_responses=False`
