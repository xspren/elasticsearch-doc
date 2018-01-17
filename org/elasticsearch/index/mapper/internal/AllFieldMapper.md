### public class AllFieldMapper extends MetadataFieldMapper

解析 Index _all 字段中的内容

##### public interface IncludeInAll

##### String Name = "_all"

##### String CONTENT_TYPE = "_all"

##### public static class Builder extends MetadataFieldMapper.Builder<Builder, AllFieldMapper>

#####  static final class AllFieldType extends MappedFieldType

  * public AllFieldType() 初始化 FileldDataType 类型为 "string"
  * public AllFieldType(AllFieldType ref)
  * String typeName() // _all

#####  public static class TypeParser implements MetadataFieldMapper.TypeParse

###### parse 方法

  ```java
  // parseField below will happily parse the doc_values setting, but it is then never passed to the AllFieldMapper ctor in the builder since it is not valid. Here we validate the doc values settings (old and new) are rejected
  ```

  针对 allFieldMapper 的属性进行解析：

  ```
  <font color=red> 需要注意的是如果是如果是通过 template 生成索引，在创建 template 时候不会对这些字段进行校验，在创建的时候才会真正的校验所有字段中的设置是否合法 </font>
  ```

  * 判断是否包含 doc_values 字段，如果包含则抛出异常
  * 判断是否包含 fielddata 字段，如果 format 字段的值为 "doc_values" 则抛出异常
  * 解析该字段的属性，并且设置 builder 的属性或对 builder 进行操作
  * 对参数进行设置后返回 builder
