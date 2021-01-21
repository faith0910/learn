##### redis的淘汰策略

4.0之前:

​				allkey-random	 	在所有键值对中随机淘汰

​				allkeys-lru                 在所有键值对中淘汰最近最少使用的

​				noeviction                不淘汰数据（默认）

​				volatile-lru                在设置了过期时间的键值对中淘汰最久未使用的

​				volatile-random      在设置了过期时间的键值对中随机淘汰

​				volatile-ttl				优先淘汰更早过期的键值

4.0之后:

​				allkeys-lfu				在所有键值对中淘汰使用频率最少的

​				volatile-lfu				在设置了过期时间的键值对中淘汰使用最少的键值

​	