#MyBatis通用Mapper3

##极其方便的使用MyBatis单表的增删改查

##支持单表操作，不支持通用的多表联合查询

##优点?

通用Mapper都可以极大的方便开发人员。可以随意的按照自己的需要选择通用方法，还可以很方便的开发自己的通用方法。

##MyBatis工具网站:[http://mybatis.tk](http://www.mybatis.tk)

##特别强调：不是表中字段的属性必须加`@Transient`注解

##项目文档

###在你打算使用通用Mapper前，一定要看看下面的文档，许多人在初次使用时遇到的问题，99%都在文档中有说明！！

1. [如何集成通用Mapper](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/2.Integration.md)

2. [如何使用通用Mapper](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.Use.md)

2. [3.3.0版本新增功能用法文档](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.2.Use330.md)

3. [根据需要自定义接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/4.Professional.md)

4. [Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)

5. [扩展通用接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/6.MyMapper.md)

6. [使用Mapper专用的MyBatis生成器插件](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/7.UseMBG.md)

7. [在Spring4中使用通用Mapper](http://git.oschina.net/free/Mapper2/blob/master/wiki/mapper/4.Spring4.md)

8. [Mapper3常见问题和用法](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/9.QA.md)

##通用Mapper - 简单用法示例

全部针对单表操作，每个实体类都需要继承通用Mapper接口来获得通用方法。

示例代码：

    CountryMapper mapper = sqlSession.getMapper(CountryMapper.class);
    //查询全部
    List<Country> countryList = mapper.select(new Country());
    //总数
    Assert.assertEquals(183, countryList.size());

    //通用Example查询
    Example example = new Example(Country.class);
    example.createCriteria().andGreaterThan("id", 100);
    countryList = mapper.selectByExample(example);
    Assert.assertEquals(83, countryList.size());

    //MyBatis-Generator生成的Example查询
    CountryExample example2 = new CountryExample();
    example2.createCriteria().andIdGreaterThan(100);
    countryList = mapper.selectByExample(example2);
    Assert.assertEquals(83, countryList.size());

CountryMapper代码如下：

    public interface CountryMapper extends Mapper<Country> {
    }

这里不说更具体的内容，如果您有兴趣，可以查看下面的<b>项目文档</b>

##实体类注解

从上面效果来看也能感觉出这是一种类似hibernate的用法，因此也需要实体和表对应起来，因此使用了JPA注解。更详细的内容可以看下面的<b>项目文档</b>。

Country代码：

    public class Country {
        @Id
        private Integer id;
        @Column
        private String countryname;
        private String countrycode;
        //省略setter和getter方法
    }
    
[使用Mapper专用的MyBatis Generator插件](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/7.UseMBG.md) 可以方便的生成这些（带注解的）实体类。

##通用Mapper支持Mybatis-3.2.4及以上版本

##使用Maven

###重要提示,3.1.0及以后版本的groupId修改为tk.mybatis，artifactId为mapper

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper</artifactId>
    <version>3.3.9</version>
</dependency>
```

##引入Jar包，下载地址：

https://oss.sonatype.org/content/repositories/releases/tk/mybatis/mapper

http://repo1.maven.org/maven2/tk/mybatis/mapper

由于通用Mapper依赖JPA，所以还需要下载persistence-api-1.0.jar：

http://repo1.maven.org/maven2/javax/persistence/persistence-api/1.0/

##[更新日志](http://git.oschina.net/free/Mapper/blob/master/wiki/Changelog.md)

##最新版本3.3.9 - 2016-09-04

* 增加`selectByIds`和`deleteByIds`，用法见通用Mapper接口大全
* MBG插件支持`beginningDelimiter`和`endingDelimiter`
* MBG插件增加schema配置（catalog也可以用这个），会自动在表的注解名字前面加上`schema.tablename`
* MBG插件支持oracle获取注释，其他数据库可以尝试#114
* 根据**李领北**建议修改`Example`中的`propertyMap`#159
* [MyBatis Generator 1.3.4 扩展，可以设置 Mapper（Dao）后缀](http://blog.csdn.net/isea533/article/details/52430691)
* 增加对MBG1.3.4的支持
* `Example`中的`andIn`和`andNotIn`中的参数`Collection`改为`Iterable`
* 解决驼峰转下划线的错误，感谢 ptma, piggsoft 和 liufor 的PR

###3.3.8 - 2016-03-23

* `Example`的`andIn`和`andNotIn`方法参数改为`Collection` #109
* 解决ResultMapping.Builder3.2.6版本新增`lazy`方法导致无法兼容3.2.4~3.2.5版本的问题，仍然兼容3.2.4+
* 解决github[#12](https://github.com/abel533/Mapper/issues/12) 问题
* 解决#107
* 解决和分页插件PageHelper中orderBy默认属性名相同导致排序的错误

###3.3.7 - 2016-03-12

* `Example`增加`orderBy`方法，使用属性进行排序，例如：`example.orderBy("id").desc().orderBy("countryname").orderBy("countrycode").asc();`
* 当实体类包含数组类型的字段时，在`resultMap`中不使用`javaType`，这种情况如果出错，可以通过`@ColumnType`注解设置`jdbcType` #103
* 实体类中忽略`transient`类型的字段#106

###3.3.6 - 2016-02-20

* 增加对mybatis-spring 1.2.4版本的支持，兼容之前的版本

###3.3.5 - 2016-02-16

* `Example`增加对动态表名支持，通过`setTableName`设置表名
* 在example相关的两个`update`方法中，参数为实体类和`Example`，这个方法只能通过`Example`来设置动态表名，不支持通过实体设置动态表名
* 优化两个`select count`查询，当表只有一个主键的时候，使用`select count(pk)`，其他时候使用`select count(*)`

###3.3.4 - 2016-01-05

* 解决insertList的bug#86
* `Example`构造方法增加`notNull`参数，默认`false`，允许值为`null`，值为`null`的时候不加入到条件中。
* `seqFormat`格式化参数增加第四个可配置值`TableName`，该属性的具体含义请参考[如何集成通用Mapper](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/2.Integration.md)中的参数介绍

###3.3.3 - 2015-12-30

- 解决OGNL中的and,or大写导致的错误
- 解决SpecialProvider不支持insertable的bug#77
- 解决JDK6,7无法获取字段泛型类型的问题。
- 提供一个Spring Boot集成的示例: https://github.com/abel533/MyBatis-Spring-Boot

###3.3.2 - 2015-12-12

- 解决数据越界bug#73
- 解决and少空格问题
- 解决order by错误#74
- `tk.mybatis.spring.mapper.MapperScannerConfigurer`中的属性`mapperHelper`增加setter和getter方法，方便通过代码进行配置

###3.3.1 - 2015-12-09

- 增加`enableMethodAnnotation`参数，可以控制是否支持方法上的JPA注解，默认`false`。
  设置`enableMethodAnnotation = true`的时候注意，如`getRealName`或`setYourName`都会产生`realName`属性或`yourName`属性，如果该方法对应的属性不是表中的字段，就需要给方法增加`@Transient`注解。
  同样如果你的实体是继承`Map`类型的，你不需要在实体中写`private String userName`这样的属性，你只需要写`setUserName`或`getUserName`这样的方法就可以。
- 在处理的注解的时候，优先从`Field`获取，然后是`setter`方法，最后是`getter`方法，注解重复的情况下，只获取按顺序得到的第一个
- 为了支持如`public class Country extends Entity<Integer, String>`这样的泛型类型,在生成`#{propertyName}`的时候都带上了`javaType`属性。
  产生的结果就是`#{propertyName, javaType=java.lang.Integer}`这样子的，这会导致当你调用方法时，必须保证类型一致。
  也就是假设主键是`Integer id`，调用`selectByPrimaryKey(Object id)`的时候，参数`id`必须使用`100`这样的数字，不能使用`"100"`字符串（以前版本可以）。
  如果不带`javaType`，那么如果`id`是个泛型，MyBatis查找的时候就会因为找不到正确的类型而抛出异常。
- 为了让扩展更方便，将`tk.mybatis.mapper.provider`包下所有的通用接口的实现方法改为了`String`形式。
  自己扩展单表操作的方法是非常容易的事情，建议有一定通用Mapper使用基础的自行扩展，扩展可以参考[如何扩展通用接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/6.MyMapper.md)
- 新增`SqlHelper`工具类，其中包含了大量可用的现成的SQL方法
- `@Column`注解增加对`insertable`和`updatable`属性的支持

###3.3.0 - 2015-11-01

- 增加对动态表名的支持，需要实体类继承`IDynamicTableName`接口，用法见[详细说明](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.2.Use330.md)

- `Example`增加自定义查询条件，提供了4个方法，具体方法和用法见[详细说明](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.2.Use330.md)

- 新增`@ColumnType`注解，可以单独设置列的`jdbcType`和`typeHandler`

- `Example`的`in`和`not in`中的`List<Object>`参数改为`List<?>`，允许任意类型

- select查询方法返回类型不在使用`resultType`，改为`resultMap`，因此可以支持`typeHandler`的读取

- `Style`自动转方式新增`camelhumpAndUppercase`驼峰转下划线大写形式,`camelhumpAndLowercase`驼峰转下划线小写形式

- MapperTemplate中的`getSelectReturnType`方法改为`getEntityClass`，`getBEFORE`改为`isBEFORE`

- 文档中增加`@GeneratedValue(strategy = GenerationType.IDENTITY)`的一种重要[用法说明](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/3.2.Use330.md)

- 修复selectAll不支持`@OrderBy`注解的bug

- 解决一个驼峰转换bug，例如`helloWorld`会转换为`hello_world`（原先是`hello_World`）


###3.2.2 - 2015-09-19

* 和Spring集成时，允许通过`markerInterface`属性配置通用接口（注意该属性的原有作用不变），想要让该接口自动注册，该接口就需要继承`tk.mybatis.mapper.common.Marker`接口，`Mapper<T>`默认继承该接口，所以如果自己的接口是继承`Mapper<T>`的，不需要再继承。
* 解决注册默认接口时存在的bug

###3.2.1 - 2015-09-02

* 解决spring集成中可能出现definition.getBeanClassName()空指针异常bug[#49](http://git.oschina.net/free/Mapper/issues/49)
* 关于3.2.x版本，请仔细看3.2.0的更新日志，最新版本的文档也是针对3.2.x版本的

###3.2.0 - 2015-09-01

* 移除`MapperInterceptor`拦截器，以后不能在通过拦截器配置
* 增加mybatis-spring特殊支持，主要是根据mybatis-spring项目增加了下面两个类：
   - `tk.mybatis.spring.mapper.MapperScannerConfigurer`
   - `tk.mybatis.spring.mapper.MapperFactoryBean`
* 这两个类和MyBatis提供的区别是增加了MapperHelper属性，通过在`MapperScannerConfigurer`中使用`properties`属性注入配置
* 这两个类，在全名上和MyBatis的区别是`org.mybatis.xxx`改为了`tk.mybatis.xxx`，名字相近，更方便修改配置
* 和Spring集成方法：

```xml
<bean class="tk.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.isea533.mybatis.mapper"/>
    <property name="properties">
        <value>
            mappers=tk.mybatis.mapper.common.Mapper
        </value>
    </property>
</bean>
```

* 这种配置方式是不是简单的不能再简单了?
* 增加`style`属性配置，用来配置对象名/字段和表名/字段之间的转换方式，可选值：
   - `normal`:使用实体类名/属性名作为表名/字段名
   - `camelhump`:<b>这是默认值</b>，驼峰转换为下划线形式
   - `uppercase`:转换为大写
   - `lowercase`:转换为小写
* 增加实体注解`@NameStyle`，该注解优先于全局配置`style`
* 解决`example.selectProperties`映射错误的bug[#48](http://git.oschina.net/free/Mapper/issues/48)


###3.1.3 - 2015-08-25

* 去掉了3.1.3-SNAPSHOT版本中的`MapperOnceInterceptor`拦截器，下个版本会完善`MapperHelper`的配置方式
* `Example`增加了`example.selectProperties("id", "countryname", ...)`方法，可以指定查询列，注意这里参数写的是属性名，`Example`会自动映射到列名
* `Example`增加`andEqualTo(实体对象)`方法，可以将一个实体放进去，会自动根据属性和值拼出column=value的条件 <b>Bob - 0haizhu0@gmail.com 提供</b>
* MyBatis在处理`<cache/>`和`@CacheNamespace`的时候不统一，只有一个能生效，这导致xml中配置二级缓存对通用Mapper注解形式的方法无效，该问题已解决
* 二级缓存配置方法，如果接口有对应的xml，在xml中配置二级缓存。如果只有接口没有xml，用注解配置二级缓存即可
* 需要注意的是，二级缓存在xml配置时，只对通用Mapper方法有效，自己用`@Select`等注解定义的这种仍然无效，这种情况只能在xml中定义

###3.1.2 - 2015-07-14

* 解决别名时的一种特殊情况，例如`@Column(name="`desc`")`的时候，就不需要自动添加别名
* 反射获取所有列名的时候，不在自动转换为大写形式，对数据库区分大小写的情况有用

###3.1.1 - 2015-07-01

* 解决`ConditionMapper`中`selectByCondition`和`updateByCondition`方法错误

###3.1.0 - 2015-06-10

* 基础包名从`com.github.abel533`改为`tk.mybatis.mapper`
* Maven的groupId改为`tk.mybatis`,artifactId为`mapper`
* 增加和Example功能类似的Condition查询，仅仅名字不同
* 更多详细变化请看[Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)
* 关于3.0.x版本请看[Mapper3.0.x](http://git.oschina.net/free/Mapper/tree/Mapper3.0.x/)

###3.0.0 - 2015-06-04

* 将`EntityMapper`和`SqlMapper`移出，现在是独立项目[EntityMapper](http://git.oschina.net/free/EntityMapper)
* 将`Mapper<T>`全部接口方法拆分为独立接口，方便选择集成
* 增加`MySqlMapper<T>`包含批量插入和单个插入，批量插入可以回写全部id
* 增加`RowBoundsMapper<T>`包含两个分页查询，可以配合[PageHelper](http://git.oschina.net/free/Mybatis_PageHelper)实现物理分页
* 详细变化请看[Mapper3变化](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/1.Changes.md)
* Mapper2资深用户请看[Mapper3高级应用](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/4.Professional.md)
* [Mapper3通用接口大全](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/5.Mappers.md)
* [快速开发自己的通用接口](http://git.oschina.net/free/Mapper/blob/master/wiki/mapper3/6.MyMapper.md)

##作者信息

作者博客：http://blog.csdn.net/isea533

作者邮箱： abel533@gmail.com

Mybatis工具群： <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=29e4cce8ac3c65d14a1dc40c9ba5c8e71304f143f3ad759ac0b05146e0952044"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="Mybatis工具" title="Mybatis工具"></a>

推荐使用Mybatis分页插件:[PageHelper分页插件](https://github.com/pagehelper/Mybatis-PageHelper)