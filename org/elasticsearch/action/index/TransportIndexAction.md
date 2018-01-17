# TransportIndexAction

```
预先对 index 进行操作
如果 autoCreateIndex 参数为 true 时会自动创建一个索引如果这个索引不存在
默认参数为 true
如果 allowIdGeneration id 没有进行设置，将生成一个 id， 默认配置为 ture
```



### extends TransportReplicationAction<IndexRequest, IndexReuqest, IndexResponse> 



### doExcute

#### parameters

* final IndexRequest request
* final ActionListener<IndexRespoonse> listener

#### return

void

#### 业务流程

* 获取 ClusterState 集群状态
* 检查是否允许自动创建索引（包括一些 alias 检查，系统配置检查）
* 创建 CreateIndexRequest 类，设置 index 名称，mapping 名称



