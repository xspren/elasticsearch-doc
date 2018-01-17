# IndicesService

### function createIndex

#### parameters

* String sIndexName 索引名称
* Settings settings 设置
* String 本地集群节点 id

#### return

IndexService

#### 操作流程

* 判断当前 lifecycle 是否为启动状态，如果没有启动则抛出异常
* new Index 并且在构造方法中赋值 sIndexName
* 检查 Indices 中是否包含当前的 Index 如果包含则抛出 IndexAlreadyExistsException 异常
* indicesLifecycle.befoireIndexCreated(index, settings) 在创建索引之前做一些准备工作（貌似没有做什么实际操作）
* 创建 indexSettings, 先设置系统 settings 然后将定制化的 settings 覆盖原来的系统设置 settings
* 创建 ModulesBuilder modules
* pluginsService.processModules(modules) 如果有 plugin 则进行 service 处理
* 创建 indexInjector // 这块暂时没有看懂
* 或者 indexService 实例
* 将 indexService 添加到 indices map 中， key 为 index 名称
* 返回 indexService

