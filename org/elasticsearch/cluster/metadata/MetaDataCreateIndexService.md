# MetaDataCreateIndexService

Extends AbstractComponent

该类负责提交创建索引请求

### MAX_INDEX_NAME_BYTES

索引长度限制

### function validateIndexName

对创建的 index 名称进行校验

#### parameters

* String index 索引名称
* ClusterState state 集群状态

#### return

void

#### 流程分析

* 校验路由表（routingTable）中是否包含新建的 Index 名称，存在则抛出异常
* 检验元数据（metadata）中是否包含新建 index 名称，存在则抛出异常
* 校验 index 名称合法性（不能包含非法字符，不能包含 #，不能以 _ 开头，必须全部小写）
* 检验是否使用 UTF-8 字符集
* 校验是否超过 index 最大长度限制
* 校验是否与 alias 重名
* 校验是否为 ‘.’ 或者 ‘..’

### function validateIndexSettings

校验 Index 配置合法性

#### parameters

* String indexName 索引名称
* Settings settings 索引配置

#### return

void

#### 流程分析

* 调用 getIndexSettingsValidationErrors 校验错误
* 如果错误列表中存在错误则抛出异常

### function getIndexSettingsValidationError

校验 index 配置错误

#### parameters

* Settings settings 配置参数

#### return

void

#### 流程分析

* 通过 index.data_path 获取索引路径，
* 如果 customPath 不为空并且 evn.sharedDateFile 为空则在错误列表中添加错误信息
* 如果 customPath 不为空则判断这个 path 是不会 evn.sharedDataFile 的子路径，如果不是则在错误列表中添加错误信息
* 获取 parimary shards 个数设置和 replicas 个数设置
* 如果两个个参数不为空但是小于 0 则抛出异常
* 返回设置校验

### function validate

校验索引是否合法

#### parameter

* CreateIndexClusterStateUpdateRequest request 创建索引请求
* ClusterState state 集群状态

#### return

void

#### 流程分析

* validateIndexName
* validateIndexSettings

### function findTemplates

查找模板信息

#### parameters 

* CreateIndexClusterStateUpdateRequest request 
* ClusterState state 当前集群状态
* IndexTemplateFilter indexTemplateFilter 索引模板过滤器

#### return

List<IndexTempateMetadata>

#### 流程分析

* 从当前 ClussterState 中循环获取模板信息
* 使用正则匹配检查 Index 名称是否匹配当前 cluster 模板名称
* 如果匹配添加到 templates 列表中
* 对 template list 中元素的 order 属性进行排序判断，优先级最高的放在首位
* 返回 List

### function createIndex

用于创建索引

#### Parameter

* CreateIndexClusterStateUpdateRequest request
* final ActionListener<ClusterStateUpdateResponse> listener

#### return

void

#### 流程分析

* 创建一个新的 Settings

* 将请求中以 Index. 开头的所有配置提取出来并且赋给 request

* 提交变更集群状态申请（clusterService.submitStateUpdateTask）

* 实现 AckedClusterStateUpdateTask

  * 实现 execute 方法
  * 校验索引是否合法 validate
  * 校验 alias 是否合法
  * 获取 template 列表 (findTemplates)
  * 将 request 中的 mappings 复制到 mappings<String, Map<String, Object>> map 中 

  ```
  request.mappings() 中应该是只保存了请求中的日志 type 类型，所以在初始化过程中 mappings map 中的 applog 模板的 properties 字段为空
  ```

  ​

  *  循环迭代已经查到到的 templates List
    * 首先在 templatesNames 中添加 template name
    * 查看请求中的 template name 是否与 mappings 中的模板名称匹配
    * 如果匹配对 mappings 进行解析，将 mapping 的配置复制到 mappings 中
    * 如果不存在则使用当前的 mappings
    * 检查是否存在匹配的 template.customs() 
    * 检查是否存在匹配的 template.aliases() 并且赋值
  *  创建一个默认的 Settings.Builder
  *  将 templates 中的配置赋值给 indexSettingsBuilder
  *  将 request 中的新增 settings 覆盖到 indexSettingsBuilder 中，如果 request 中的 settings 为空则跳过改设置
  *  对一些必要参数进行判断，如果发现设置为空则设置一些默认值给 indexSettingsBuilder
  *  给 indexSettingsBuilder 设置一个随机的 uuid
  *  调用 indexService.createIndex 方法创建索引。在 master 节点上创建并且校验节点的合法性，并将其加入到 mapping 中去


  * 通过 indicesService.indexServiceSafe 方法获取到这个索引的 indexService
  * 获取当前 indexService 的 mapperService
  * 判断 mappings 中是否包含 '_default' mapping, 如果存在 default mappings 则将一份压缩的 default mapping 设置到 mapperService 中
  * for 循环 mappings.entrySet()
    * 如果 key 为 _default 跳过该步骤、
    * 在 mapperService 中设置压缩后的 mapping （_all, properties 等属性）


  * 判断 request 中是否包含 alias 属性，否则对 alias 属性进行判断, 如果存在 alias 则对 alias 进行校验
  * ​
  * 判断 request.state() 状态是否为 OPEN 装填
  * 将新的索引信息添加到集群的 routingTable
  * 动态注入 IndexShardModule, StoreModule, DeletionPolicyModule
  * 在注入 StoreMoudle 时候通过 FSDirectoryService 的 newDirectory 生成索引储存目录
