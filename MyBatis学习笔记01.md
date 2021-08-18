---
title: MyBatis学习笔记01
toc: true
date: 2021-06-30 10:20:38
tags:
index_img:
categories:
---
# 学习内容
## 学习资源
[狂神说MyBatis教学视频](https://www.bilibili.com/video/BV1NE411Q7Nx) P01~P08
## Java项目结构
```
├── Mybatis01
│   ├── mybatis-01
│   │   ├── pom.xml
│   │   └── src
│   │       ├── main
│   │       │   ├── java
│   │       │   │   └── com
│   │       │   │       └── xxxx
│   │       │   │           ├── dao
│   │       │   │           │   ├── UserMapper.java
│   │       │   │           │   └── UserMapper.xml
│   │       │   │           ├── pojo
│   │       │   │           │   └── User.java
│   │       │   │           └── utils
│   │       │   │               └── MybatisUtils.java
│   │       │   └── resources
│   │       │       ├── mybatis-config.xml
│   │       │       └── db.properties
│   │       └── test
│   │           └── java
│   │               └── com
│   │                   └── xxxx
│   │                       └── dao
│   │                           └── UserMapperTest.java
│   ├── mybatis-02
│   ├── pom.xml
```

## 项目各包详细说明
### 项目结构说明
- `mybatis-01`和`mybatis-02`是项目`Mybatis01`下的两个子项目
  - 外层的`pom.xml`中的Maven配置会被应用到所有子项目中
    - 子项目中也有自己的`pom.xml`
  - `src`目录下分`main`和`test`两部分进行代码书写
    - `main`目录下分`java`和`resources`
      - `java`目录下放负责数据交互的包`dao`，实体类包`pojo`，工具类包`utils`     
      - `resources`目录下放属性配置文件`db.properties`和XML配置文件`mybatis-config.xml`

### dao
`dao`包下：一个`Interface`文件`Mapper`和它对应的`Mapper.xml`
- `Mapper`中写数据层接口
- `Mapper.xml`中写sql语句：
  - `<mapper namespace="com.xxxx.dao.UserMapper">`：sql语句都写在mappe标签内部，`namespace`的值是对应接口文件
    - `select`、`update`、`insert`、`delete`

### pojo
`pojo`包下：实体类设计  
起码有最基本的`getter()` `setter()` `toString()`，可以用右键-><kbd>Generate</kbd>(<kbd>command</kbd>+<kbd>n</kbd>)来一键生成
```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```

### utils
`utils`包下：工具类  
例如在`MybatisUtils`的类中调用[`mybatis-config.xml`](#3)，进行sqlSession的生成
```java
public class MybatisUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        InputStream inputStream = null;
        try {
            //使用Mybatis第一步：获取SqlSessionFactory对象
            String resource = "mybatis-config.xml";
            inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    //SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。
    //可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
    public static SqlSession getSqlSession() {
        return sqlSessionFactory.openSession();
    }
}
```

### mybatis-config.xml

<span id="3">`mybatis-config.xml`中写对MyBatis系统的核心设置</span>：[MyBatis官方XML配置指南](https://mybatis.org/mybatis-3/zh/configuration.html)
- 最外层标签：`<configuration>`，配置写在其中
  - `<properties resource="db.properties">`：引入外部配置文件[db.properties](#1)，可在外部配置文件中配置一些值，以在此xml文件中引用这些值，例如[数据库的driver、url、username、password](#2)
  - `<typeAliases>`：在此标签下设置类型别名，可单独设置也可批量设置
    - 单独设置：`<typeAlias alias="你想好的别名" type="com.xxxx.pojo.要起别名的类"/>`
    - 批量设置：`<package name="com.xxxx.pojo"/>`  自动为`pojo`包下的所有类生成别名，别名设置为各实体类的名字（首字母大小写都可）
  - `<enviroments>`：可以为项目配置多套环境，以供不同场景的使用。使用`default`字段制定默认使用的环境。
    - `<enviroment id="此环境名">`
      - `<transactionManager type="JDBC"/>`：暂时就这样写
      - `<dataSource type="POOLED">`：`type`表示数据源类型，下面是数据源属性的配置

#### dataSource
属性配置可以是这样
```xml
<property name="driver" value="com.mysql.jdbc.Driver"/>
<property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=false&amp;characterEncoding=UTF-8"/>
<property name="username" value="数据用户名"/>
<property name="password" value="数据库密码"/> 
```
  
<span id="2">**如果项目配置了`db.properties`**，可以使用其中的值简化数据源配置：</span>
```xml
<property name="driver" value="${driver}"/>
<property name="url" value="${url}"/>
<property name="username" value="${username}"/>
<property name="password" value="${password}"/>
```

### db.properties
<span id="1">`db.properties`中写数据库配置</span>:

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=false&characterEncoding=UTF-8
username=数据库用户名
pwd=数据库密码
```
## 其他
### 为项目增加一个功能的步骤
1. 在`\dao`中找到合适的interface，在其中定义好功能接口（设计方法）
2. 在`\dao`中找到对应的xml，在其中写数据库操作的实现（写sql）
3. 在测试文件中编写测试类
### 编写测试类
1. 注解`@Test`  
2. 

# 遇到的坑
- 在xml文件中进行属性值配置时，`&`要写成`&amp;`
- 在`mybatis-config.xml`中进行`dataSource`配置时，`url`的属性值中`useSSl`和`useUnicode`的值应为`false`,而不是狂神说的`true`
- 关于类型别名的三种方式：
  1. 在`java\..\pojo\User`中，在类前加注解``@Alias("你想给这个类设置的别名")``
  2. 在`resources\mybatis-config.xml`中，标签`configuration\typeAliases`中，加`<package name="想要设置别名的类所属的包"/>`，自动为包下所有的Java Bean设置别名。（例：为`com.xxxx.User`设置别名`User`和`user`）
  3. 在`resources\mybatis-config.xml`中，标签`configuration\typeAliases`中，加`<typeAlias type="想要设置别名的类" alias="你想给这个类设置的别名"/>`
  - 🗒️ Note：3存在时，1会失效；其他情况下，这几种方式的别名可以共存混用。



