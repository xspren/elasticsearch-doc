# TransportCreateIndexAction

```
创建索引 action
继承自 TransportMasterNodeAction
父类会调用该类的 masterOperation 方法执行具体的操作
```

### masterOperation

#### parameters

* CreateIndexRequest request 上层传递过来的创建索引请求
* ClusterState state 集群状态
* ActionListener<CreateIndexResponse> listener 上层注册过来的监听，主要实现了 onResponse 和 onFailure 两个方法

#### return

void 全部通过 listener 的回调返回结果

#### 流程分析

* 获取创建原因，如果没有设置 cause 为 "api"
* 校验 IndexName 是否合法如果不合法会抛出异常
* 创建 CreateIndexClusterStateUpdateRequest
* 调用 createIndexSerivce.createIndex 方法创建索引