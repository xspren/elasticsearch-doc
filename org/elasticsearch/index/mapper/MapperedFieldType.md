### public abstract class MapperedFieldType

extend FieldType (luence)

- public static class Names
  - indexName 索引名称
  - originalIndexName 真正的索引名称
  - fullName 全程
- public enum Loading
  - LAZY
  - EAGER
  - EAGER_GLOBAL_ORGINALS
  - Loading parse(String loading, Loading defaultVaule): 返回 Loading 枚举类型
- Names names
- float boost
- boolean docValues
- NamedAnalyzer indexAnalyzer // 命名解析器继承了 Luence 的 Analyzer
- NamedAnalyzer searchAnalyzer // 查询解析器
- NamedAnalyzer searchQuoteAnalyzer // 查询引用解析器
  - SimilarityProvider similarity		
  - Loading normsLoading 加载类型
- FieldDataType 字段类型
- Object nullValue
- String nullValueAsString
- public abstract String typeName() 用于返回这个字段的字段类型名称
- checkTypeName(MapperFiedldType other) 检查是否为同一类型的 type
- checkCompatibility(…) 检查是否兼容
  - ​