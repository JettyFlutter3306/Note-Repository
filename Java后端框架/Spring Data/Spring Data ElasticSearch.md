# Spring Data Elasticsearch

# 一. Spring Data Elasticsearch 简介

**Elasticsearch** 是一个实时的分布式搜索和分析引擎。它底层封装了 Lucene 框架,可以提供分布式多用户的全文搜索服务。

**Spring Data ElasticSearch** 是 **SpringData**技术对 **ElasticSearch** 原生 API 封装之后的产物,它通过对原生API的封装,使得程序员可以简单的对 **ElasticSearch** 进行各种操作。

# 二. 快速入门

## 2.1 引入依赖

```xml
<dependencies>
    <!--elasticsearch-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
    </dependency>   
</dependencies>
```

## 2.2 配置YMAL文件

```yaml
spring:
  elasticsearch:
    rest:
      uris: http://127.0.0.1:9200
```

## 2.3 编写Entity

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
@Document(indexName = "in-article")  // indexName指定索引名称
public class Article {
    @Id
    @Field(index = false, type = FieldType.Integer)
    private Integer id;
    /**
     * index:           是否设置分词,默认true
     * analyzer:        存储时使用的分词器
     * searchAnalyzer:  搜索时使用的分词器
     * store:           是否存储,默认false
     * type:            数据存储,默认FieldType.Auto
     */
    @Field(analyzer = "ik_max_word", searchAnalyzer = "ik_max_word", store = true, type = FieldType.Text)
    private String title;
    @Field(analyzer = "ik_max_word", searchAnalyzer = "ik_max_word", store = true, type = FieldType.Text)
    private String content;
    @Field(store = true, type = FieldType.Integer)
    private Integer hits;
}
```

## 2.4 编写Dao层接口

​	自定义Dao层接口继承自 **ElasticsearchRepository**，然后就获得了CRUD 方法：

```java
public interface ArticleDao extends ElasticsearchRepository<Article, Integer> {

}
```

## 2.5 测试

```java
/**
 * 创建索引,默认自动创建,不需要手动创建
 */
@Test
public void testSave() {
    Article article = new Article();
    article.setId(1)
           .setTitle("Spring Cloud")
           .setContent("老子文韬武略天下第一!");

    articleDao.save(article);
}
```

# 三. 命名规则查询

**Elasticsearch** 的命名规则和 **JPA** 基本一致:

| 关键字        | 命名规则              | 解释                       | 示例                  |
| ------------- | --------------------- | -------------------------- | --------------------- |
| and           | findByField1AndField2 | 根据Field1和Field2获得数据 | findByTitleAndContent |
| or            | findByField1OrField2  | 根据Field1或Field2获得数据 | findByTitleOrContent  |
| is            | findByField           | 根据Field获得数据          | findByTitle           |
| not           | findByFieldNot        | 根据Field获得补集数据      | findByTitleNot        |
| between       | findByFieldBetween    | 获得指定范围的数据         | findByPriceBetween    |
| lessThanEqual | findByFieldLessThan   | 获得小于等于指定值的数据   | findBy                |

# 四. 接口继承分析

这是ElasticsearchRepository接口的继承UML类图:

可以看到最顶层接口是Repository这是一个空接口,里面没有任何方法,那么其实这也就是一个标识接口 ,仅作标识使用.可以类比JDK里的Serializable, Cloneable接口.

![1645687206523](asset/es-repository.png)