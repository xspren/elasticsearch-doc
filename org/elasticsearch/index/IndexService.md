# IndexService

```
index 索引服务
```



## function createShard

### parameter

* ShardRouting routing 

### return

* IndexShard

### 代码流程

* 获取当前 shard 是否为主 shard （boolean paimary）
* 通过注入的 settingsService 获取当前 indexSettings
* 通过 routing 获取 shardId
* 判断当前索引状态是否为 closed 如果是抛出 IllegalStateException
* 尝试对当前获得当前 shard id 的操作锁
* 批量执行 indicesLifecycle 的 beforeIndexShardCreated 方法
* 首先通过 ShardPath.loadShardPath 方法通过 shardId 和 indexSettings 查找已有路径
* 如果没有则说明可能是新建索引，先循环出有多少 shard 需要创建路径，保存到 dataPathToShardCount 中