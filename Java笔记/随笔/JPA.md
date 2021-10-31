## Java Persistence API

https://juejin.cn/post/6844903822423408647

https://juejin.cn/post/6844903865284821005

JPA将Java对象的创建过程和具体的创建形式解耦。JPA规范允许程序员定义应该保留哪些对象，以及如何在Java应用程序中保留这些对象。（并非所有Java对象都需要持久化，但大多数应用程序会保留关键业务对象）

JPA本身不是一个工具或框架，它定义了一组可以由任何工具或框架实现的概念。JPA的对象关系映射（ORM）模型最初基于Hibernate。虽然JPA最初打算用于关系/SQL数据库，但是一些JPA实现已经扩展用于NoSQL数据存储。支持JPA和NoSQL的流行框架是EclipseLink，是JPA2.2的参考实现。

## 什么是Java ORM？

每个JPA实现都提供某种ORM层

![img](https://user-gold-cdn.xitu.io/2019/4/16/16a24b5e43d738f6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

对象关系映射是一项任务——开发人员有充分的理由避免手动执行。像Hibernate ORM或EclipseLink这样的框架将该任务编码为库或框架，即ORM层。

ORM层作为应用程序体系结构的一部分，负责管理软件对象的转换，以便与关系数据库中的表和列进行交互。在Java中，ORM层转换为Java类和对象，以便可以在关系数据库中存储和管理它们。

### 配置Java ORM层

设置新项目以使用JPA时，需要

- 配置数据存储区和JPA提供程序。
- 配置数据存储连接器以连接到所选择的数据库（SQL或NoSQL）。
- 配置JPA提供程序，如Hibernate或EclipseLink。
- 也可手动配置JPA，但许多开发人员选择使用Spring的开箱即用支持。

### Java数据对象

Java Data Object是一个标准化的持久性框架，它与JPA的不同之处主要在于支持对象中的持久性逻辑，以及它长期以来对使用非关系数据存储的支持。

JPA与JDO足够相似，JDO提供者也经常支持JPA（Apache JDO项目）。

### Java中的数据持久性

ORM层是一个适配器层：它使对象图的语言适应SQL和关系表的语言。ORM层允许面向对象的开发人员构建持久保存数据的软件，而无需离开面向对象的范例。

使用JPA时，可以创建从数据存储区到应用程序的数据模型对象的映射。你可以定义对象和数据库之间的映射，而不是定义对象的保存和检索方式，然后调用JPA来保存它们。

如果你正在使用关系数据库，那么应用程序代码和数据库之间的大部分实际连接将由**JDBC**（Java数据库连接API）处理。

作为规范，JPA提供元数据注释，我们可以使用它来定义对象和数据库之间的映射。每个JPA实现都为JPA注释提供了自己的引擎。

JPA规范还提供了`PersistanceManager`和`EntityManager`，它们是与JPA系统联系的关键点。

## Mybatis和JPA（SpringDataJPA或Hibernate）的区别

eg. 现在业务上有A、B、C、D四个表。如果每个表都会在业务中用到，都需要有单独的CRUD，虽然有一定的关联关系，但是这种情况用JPA就比较合适。

ABCD四个Java实体，每个实体都对应一个repository，然后在repository层进行CRUD编写。

但是如果业务上A、B、C、D四个表是关联关系，且几乎不会单独对A、B、C进行操作，而展先出来的也是D，那么这个时候JPA的使用就会很麻烦，因为在一个接口四个repository挨个一次。还要考虑原子性这些，用Mybatis就比较合适。

## 搭建项目

使用Spring Boot构建项目

### 1. 引入依赖

同时引入Spring Data JPA和Mybatis两个ORM框架的依赖包

通过Spring Data JPA可以轻松使用Hibernate框架。

``` xml
<!-- JPA依赖 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<!-- mabatis依赖 -->
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```



### 2. 定义实体类 User.java

定义一张table_name:

| USER_ID | USERNAME | ALIAS | AGE  |
| ------- | -------- | ----- | ---- |
|         |          |       |      |

虽然最新的SpringJPA和Mabatis都有代码生成器generation

```java
@Entity
@Table(name = "table_name")
@Data			//lombok语法，自动生成类属性getter、setter和toString方法
@NoArgsConstructor	//自动为类生成无参构造函数
@AllArgsConstructor		//自动为类生成全部属性的构造函数
public class User implements Serializable {
    private static final long serialVersionUID = 78558262691294481727L;
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "USER_ID")
    private Long id;
    
    @Column(name = "USERNAME")
    private String username;
    
    @Column(name = "ALIAS")
    private String alias;
    
    @Column(name = "AGE")
    private int age;
}
```



### 3. 定义数据持久化接口

Spring Data JPA和Mybaties的持久化接口定义如下

JPA：

```java
import org.springframework.data.jpa.repository.JpaRepository;
import xxx.User;

public interface UserRepository extends JpaRepository<User, Long> {}
```

Mybatis：

```java
import xxx.User;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Component;

@Component  //标注此接口，Spring会自动扫描此配置类
@Mapper //把这个mapper的DAO交由Spring进行管理
public interface UserMapper {
    User selecrByUserId(Long id);
    List<User> selectAllUser();
}
```

### 4. 定义UserService

定义接口UserService：

```java
public interface UserService {
    
    User save(User user);
    
    User findById(Long id);
    
    List<User> findAll();
}
```

接口实现UserServiceImpl：

```java
@Service("userService") //声明该类是一个事务提供类，且设置该类被Spring初始化时Bean对象名称为“userService”。
public class UserServiceImpl implements UserService {
    
    private UserRepository userRepository;
    
    private UserMapper userMapper;
    
    @Autowired
    UserServiceImpl(UserRepository userRepository, UserMapper userMapper) {
        this.userRepository = userRepository;
        this.userMapper = userMapper;
    }
    
    @Override
    @Transcational //用于设置每个方法的事务控制方式
    public User save(User user) {
        return userRepository.save(user); //JPA方法
    }
    
    @Override
    @Transactional(readOnly = true)
    public User findById(Long id) {
        return userMapper.selectByUserId(id);
    }
    
    @Override
    @Transactional(readOnly = true)
    public List<User> findAll() {
        return userMapper.selectAllUser();
    }
}
```



### 5. 定义控制器

在UserServiceImpl.java文件中，我们实现了对单个数据表用JPA读取，用Mybatis增、查过程。

接下来在控制器中去定义访问：

```java
@RestController
@RequstMapping("/users")
public class UserController {
    private UserService userService;
    
    @Autowired
    UserController(UserService userService) {
        this.userService = userService;
    }
    
    @PostMapping
    public User save(@RequestBody User user) {
        return userService.save(User);
    }
    
    @GetMapping("/{id}")
    public User findOne(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    @GetMapping
    public List<User> findAll() {
        return userService.findAll();
    }
}
```



