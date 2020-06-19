# spring boot + mybatis框架搭建

## 1、创建springboot项目

## 2、写配置文件

在resources文件夹下创建application.yml文件

```yml
server:
  port: 8000

spring:
  datasource:
    name: springboot
    type: com.alibaba.druid.pool.DruidDataSource
    #druid相关配置
    druid:
      #监控统计拦截的filters
      filter: stat
      #mysql驱动
      driver-class-name: com.mysql.cj.jdbc.Driver
      #基本属性
      url: jdbc:mysql://127.0.0.1:3306/springtest?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
      username: root
      password: root
      #配置初始化大小/最小/最大
      initial-size: 1
      min-idle: 1
      max-active: 20
      #获取连接等待超时时间
      max-wait: 60000
      #间隔多久进行一次检测，检测需要关闭的空闲连接
      time-between-eviction-runs-millis: 60000


#mybatis配置
mybatis:
  mapper-locations: classpath:mapper/*.xml
  type-aliases-package: com.lzy.demo.entity

```

解释：

​	a. 我们实现的是spring-mybatis的整合，包含mybatis的配置以及datasource数据源的配置当然属于spring配置中的一部分，所以需要在`spring:`下。

​	b. `mapper-locations`相当于XML中的`<property name="mapperLocations">`用来扫描Mapper层的配置文件，由于我们的配置文件在`resources`下，所以需要指定`classpath:`。

​	c.  `type-aliases-package`相当与XML中`<property name="typeAliasesPackase">`别名配置，一般取其下实体类类名作为别名。

​	d.  `druid`代表本项目中使用了阿里的druid连接池

## 3、pom文件

```xml
		<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.2</version>
        </dependency>
		 <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
		 <!-- alibaba的druid数据库连接池 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.9</version>
        </dependency>
```

其他的可以参考其他项目的pom文件

## 4、将数据库建好

建立表tb_user，内容如图

![1571753992319](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571753992319.png)

## 5、创建实体类

<img src="C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754095796.png" alt="1571754095796" style="zoom:80%;" />

<img src="C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754133143.png" alt="1571754133143" style="zoom: 80%;" />

## 6、创建service

![1571754390563](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754390563.png)

a、基础接口

```java
public interface BaseService<T> {
    //查询所有
    List<T> findAll();

    //根据id查询
    List<T> findById(Long id);

    //添加
    void create(T t);

    //删除（批量）
    void delete(Long... ids);

    //修改
    void update(T t);
}
```

b、UserService接口

<img src="C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754302748.png" alt="1571754302748" style="zoom:80%;" />

c、创建mapper

![1571754460161](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754460161.png)

```java
@Mapper
@Component
public interface UserMapper {
    List<User> findAll();
}
```

d、在resources下创建mapper文件夹

Mapper映射文件置于`resources/mapper/`下，且置于`src/main/java/`下的Mapper接口需要用`@Mapper`注解标识，不然映射文件与接口无法匹配。

e、mapper内添加xml

![1571754799878](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571754799878.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.lzy.demo.mapper.UserMapper">

<!--    查询所有-->
    <select id="findAll" resultType= "com.lzy.demo.entity.User">
        SELECT * FROM tb_user
    </select>
</mapper>
```

注意，namespace一点要对应正确。

## 7、实现

a、在service下创建impl文件夹

b、创建UserServiceImpl.java

```java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;

    @Override
    public List<User> findAll() {
        return userMapper.findAll();
    }

    @Override
    public List<User> findById(Long id) {
        return null;
    }

    @Override
    public void create(User user) {

    }

    @Override
    public void delete(Long... ids) {

    }

    @Override
    public void update(User user) {

    }
}
```

## 8、前端展示

在controller/admin下创建UserController.java

```java
@RestController
@RequestMapping(value = "/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping(value = "/findall")
    public List<User> findAll(){
        return userService.findAll();
    }

}
```

## 9、junit测试

a、在UserServiceImpl类上右键

<img src="C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1571755486541.png" alt="1571755486541" style="zoom:80%;" />

选择JUnit4

b、在测试类上添加注解

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class UserServiceImplTest {

    @Autowired
    private UserMapper userMapper;
    @Test
    public void findAll() {
        List<User> users = userMapper.findAll();
        assertEquals(2,users.size());
    }
}
```

