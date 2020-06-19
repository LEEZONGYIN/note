# mybatis自动生成器

1、在properties文件中写入mybatis配置路径

 ![1569226130659](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1569226130659.png)

2、resources文件夹下创建mapping文件夹

3、pom文件引入自动生成mybatis的插件

```xml
 <plugin>
          <groupId>org.mybatis.generator</groupId>
          <artifactId>mybatis-generator-maven-plugin</artifactId>
          <version>1.3.5</version>
          <dependencies>
            <dependency>
              <groupId>org.mybatis.generator</groupId>
              <artifactId>mybatis-generator-core</artifactId>
              <version>1.3.5</version>
            </dependency>
            <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>5.1.30</version>
            </dependency>
          </dependencies>
          <executions>
            <execution>
              <id>mybatis generator</id>
              <phase>package</phase>
              <goals>
                <goal>generate</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <!--            允许移动生成的文件-->
            <verbose>true</verbose>
            <!--              允许自动覆盖文件-->
            <overwrite>false</overwrite>
            <configurationFile>
              src/main/resources/mybatis-generator.xml
            </configurationFile>
          </configuration>
        </plugin>
```

4、resources下创建mybatis-generator.xml

文件内容都能在网上找到

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
 
<generatorConfiguration>
 
    <context id="myBatis3" targetRuntime="MyBatis3">
 
        <!-- optional，旨在创建class时，对注释进行控制 -->
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
 
        <!--jdbc的数据库连接 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/仓库名" 							userId="数据库用户名"
                        password="数据库密码">
        </jdbcConnection>
 
 
        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
 
 
        <!-- Model模型生成器,用来生成含有主键key的类，记录类 以及查询Example类
            targetPackage     指定生成的model生成所在的包名
            targetProject     指定在该项目下所在的路径
        -->
      
        <javaModelGenerator targetPackage="com.项目名.pojo" targetProject="./src/main/java">
            <!-- 是否允许子包，即targetPackage.schemaName.tableName -->
            <property name="enableSubPackages" value="false"/>
            <!-- 是否对model添加 构造函数 -->
            <property name="constructorBased" value="true"/>
            <!-- 是否对类CHAR类型的列的数据进行trim操作 -->
            <property name="trimStrings" value="true"/>
            <!-- 建立的Model对象是否 不可改变  即生成的Model对象不会有 setter方法，只有构造方法 -->
            <property name="immutable" value="false"/>
        </javaModelGenerator>
 
        <!--mapper映射文件生成所在的目录 为每一个数据库的表生成对应的SqlMap文件 -->
        <sqlMapGenerator targetPackage="mappers" targetProject="./src/main/resources">
            <property name="enableSubPackages" value="false"/>
        </sqlMapGenerator>
 
        <!-- 客户端代码，生成易于使用的针对Model对象和XML配置文件 的代码
                type="ANNOTATEDMAPPER",生成Java Model 和基于注解的Mapper对象
                type="MIXEDMAPPER",生成基于注解的Java Model 和相应的Mapper对象
                type="XMLMAPPER",生成SQLMap XML文件和独立的Mapper接口
        -->
 
        <!-- targetPackage：mapper接口dao生成的位置 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.项目名.dao" targetProject="./src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
  <!-- 生成对应表及类名-->
        <table tableName="表名" domainObjectName="实体类名" enableCountByExample="false" enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false"></table>
 
        <!-- geelynote mybatis插件的搭建 -->
    </context>
</generatorConfiguration>
```

同时要把存放model类的包和存放dao类的包建好

5、Run -> Edit configurations ->  + -> maven

![1569228065826](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1569228065826.png)

mybatis-generator:generate

6、执行命令

![1569228145326](C:\Users\1308-Lunus\AppData\Roaming\Typora\typora-user-images\1569228145326.png)

mapping文件夹下生成mapper.xml、dataobject文件夹下（model层）生成如UserDO文件、dao文件夹下生成DOMapper文件

7、多次运行可能会造成mapper文件里重复，记得删除不然会报错