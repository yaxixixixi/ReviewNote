#GreenDao3.2.2 基本知识介绍
####介绍引入方法
>import: Add the following Gradle configuration to your Android project:   
```groovy
// In your root build.gradle file:
buildscript {
    repositories {
        jcenter()
        mavenCentral() // add repository
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.1.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
    }
}
 
// In your app projects build.gradle file:
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin
 
dependencies {
    implementation 'org.greenrobot:greendao:3.2.2' // add library
}

android{
    greendao{
            schemaVersion 2 //数据库版本
            targetGenDir 'src/main/java'
            /*默认存储在entity同级目录*/
            daoPackage 'com.msn.t2.dao'
    }
}
```

定义一个实体类，在类名声明上加上```@Entity```注解


####实体类的注解说明

- @Entity       *在实体上使用此注解会将java代码转成数据库实体，同时会引导greendao生成一些必要的代码,仅支持java代码，如果项目语言是孔天林，这里也要使用java语言*
    - schema = "my-schema" *如果有有多个schema，这里指定该实体属于哪个schema*
    - active = "true" *标记一个实体处于活动状态，一个活动的实体拥有update,refresh,delete的方法*
    - nameInDb = "Note" *标记在数据库中的表名称，默认名称基于实体类的名称*
    - indexes = {
        @Index(value = "name DESC",unique = true)
      }  *在这里指定横跨多列的索引，如果要创建单列索引直接在属性上标识@Index 见@Index*
    - createInDb *是否生成数据表，默认生成，如果已经有对应的表存在，或者多个实体对应一张表结构，设为false*
    - generateConstructors = true *是否自动生成全参数的构造，无参构造器总是存在*
    - generateGettersSetters = true *如果get/set不存在是否自动生成get/set*
- @Id(autoincrement = true) *使用long/Long属性作为数据表的id,autoincrement = true标识自增长*
- @Property(nameInDb="title") *指定在数据库中的列名称，默认名称是基于属性名的驼峰法则，如 theFirstName  --> THE_FIRST_NAME*
- @Transient    *not persisted* 
- @convert(Converter = "Converter.class",ColumnType = "String.class") *将特殊的java类型转换成数据库的类型,自定义转换类并继承PropertyConverter<C,D>*
- @ToOne        **
- @ToMany       **
- @Index(value="",name="title",unique=true) *value是以逗号分割的属性（列名）列表，为了指定顺序，可在属性后加上ASC或者DESC，name表示指定索引名称(有默认值)，unique表示是否对该索引建立唯一性约束*
- @NotNull      *对数据列指定非空约束*
- @Unique       *对数据列指定唯一性约束，注意，SQLLite会为它隐式创建索引*
- @Generated    *由greendao生成，不需开发人员更改。如果手动更改，greendao不会覆盖该代码而是打印异常信息。下方有解释*
>Constructor (see ExampleEntity:21) has been changed after generation.
>解决此问题可以通过将标记@Generated的代码还原或者删除带有@Generated的方法，删除后再次编译greenDao会重新生成
>或者将@Generated注释替换为@Keep注释，此时greenDao将不会更新此代码块，但是这将可能会影响该实体和greenDao其余部分的联系，未来greenDao也可能会想要使用不同于现在的代码
>不建议对此标记下的代码做任何更改。

> 由于id的约束，建议把业务自身的key作为附加属性添加到实体类，并为其指定唯一索引
>eg: 
```java
@Id(autoincrement=true)
private Long id;
@Index(unique=true)
private String key;
```
> 可以通过make project 或者运行 greendao task来生成相关资源代码；如果在更新实体类后遇到错误，可以尝试clean project来确保旧的构建文件清除干净  

#### 介绍核心类（翻译）
>当你在项目中定义了至少一个实体以后，greendao会生成以下三个核心类DaoMaster/DaoSession/Dao 

-DaoMaster：在指定的schema上持有数据库对象并且管理dao类。它有创建和删除表的静态方法。它的内部类OpenHelper和DevOpenHelper是由来创建数据库schema的SQLLiteOpenHelper的实现。
-DaoSession：管理一个在指定的schema上的可以通过get方法得到的dao对象。insert, load, update, refresh and delete。  
-Daos：数据访问对象持久化和查询数据  cont,loadAll and insertInTx  
根据以下实例代码
```java
daoMaster = new DaoMaster(db);
daoSession = daoMaster.newSession();
```
数据库连接属于DaoMaster，所以多个Session指向同一个数据库连接。于是，一个新的session可以快速静默的创建。然而每个session都要消耗内存通常一个session是一个实体的缓存。  


**Identity scope and session “cache”**
greenDao默认（此行为可配置）多次查询返回同一个对象的引用
如果greenDao之前查询的一个实体仍然存在于内存中（使用弱引用），再次查询时不会构建此对象，也不会创建任何数据库查询来更新实体，这个对象会马上从session中返回
  
  这里有两种清除缓存的方法：
  1、daoSession.clear();清除所有缓存
  2、noteDao = daoSession.getNoteDao();
    noteDao.detachAll();清除noteDao的缓存
>疑问
1、identity scope到底是什么
2、如果两次查询使用的查询语句不同而结果相同，是否会执行    ---猜测会执行，待测试


####API说明
#####查询sql
查询sql支持原生sql和QueryBuilder两种方法。
查询支持懒加载，在操作的数据结果集中节省内存和性能。

**QueryBuilder**






   
   
####加密
  
  
>  由于greenDao是通过反射注解类数据并保存到数据库的，所有设置默认值的方式是直接在java类中对字段设置初始值就好。  
