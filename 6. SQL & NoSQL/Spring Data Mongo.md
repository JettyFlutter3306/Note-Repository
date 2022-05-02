# Spring Data MongoDB

# 一. Spring Data MongoDB简介

MongoDB主要由文档(document)、集合(collection)、数据库(database)三部分组成

1. 文档（document）就相当于关系数据库中的一行记录
2. 多个文档组成一个集合（collection），相当于关系数据库的表
3. 多个集合组织在一起，就是数据库（database），一个 MongoDB 实例支持多个数据库



# 二.  快速入门

## 2.1 引入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
</dependencies>
```



## 2.2 编写Entity

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)\
// 此处@Document注解使用的是org.springframework.data.mongodb.core.mapping.Document, collection指定集合名称
@Document(collection = "article")  
public class Article {
    @Id
    private Integer id;
    @Field("title")
    private String title;
    private String content;
    private Integer hits;
}
```



## 2.3 编写Dao层接口

```java
public interface ArticleDao extends MongoRepository<Article, Integer> {

}
```



## 2.4 测试

​	MongoDB内部的数据是一种比较松散的接口,而且和Elasticsearch内部的数据结构一样,同样是文档型数据结构.使用Spring Data MongoDB可以动态创建数据,其实也就是没有就成创建,假如有那就是根据主键更新文档操作.

```java
@Test
public void testSave() {
    Article article = new Article();
    article.setId(1)
           .setTitle("来了来了")
           .setContent("我霍啸林文韬武略,天下第一!")
           .setHits(100);

    articleDao.save(article);
}
```



# 三. 命名规则查询

Spring Data MongoDB的命名规则查询和其他的都一样,也遵循和Spring Data JPA一样的规则.照葫芦画瓢就行.