# primary-ssm-web  

##0.项目概览                
    利用IDEA（或STS）构建Maven项目pom.xml导入dependency，spring-boot-starter-thymeleaf（thymeleaf），spring-boot-starter-web（Spring MVC）     mybatis-spring-boot-starter（Mybatis），mysql-connector-java（MySQL 驱动）。
   
##1.Navicat建库建表（创建数据库“schooltest”，包含表“stu”，里面有字段“stu_id,stu_name,stu_password,stu_des”）
     
    show databases;
    drop database if exist schooltest;
    create database schooltest;
    show databases;
    use schooltest;
    create table stu(stu_id int primary key auto_increment,
                   stu_name varchar(50) not null unique,
                   stu_password varchar(50) not null,
                   stu_des varchar(50)  comment '描述信息'
                   );
    show tables;
    describe stu;
    
    insert into stu values(0,"cai","9420","handsome");
    insert into stu(stu_id,stu_name,stu_password) values(null,"可口可乐","@$#%^&123");
    insert into stu(stu_name,stu_password) values('hhhh',"GUGU654");      //自增长的主键id可写（写就用0或null填补），不写也行（其自动补齐）
    
    
     select * from stu where stu_name like '%口%'; //模糊查询
     
     update stu set stu_name="modify" where stu_name="hhh";
     
     delete from stu where stu_name like '%h';
     
 ##1.5 配置resource目录下的application.properties（url：ip+port，编码方式，注意serverTimezone）
 
     spring.datasource.url=jdbc:mysql://localhost:3306/tryssm?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC
     spring.datasource.username=root
     spring.datasource.password=11111111
     spring.datasource.driver-class-name=com.mysql.jdbc.Driver
    
 ##2.POJO类的书写和DAO持久层的书写（利用注解实现）
 
     POJO类用来匹配数据表，成员变量匹配表中字段，生成getter、setter方法，传输和存储时需要实现序列化接口；     
     DAO层为接口，需要有 实现类 或 xml文件（完成复杂SQL） 或 注解（完成简单SQL） 实现接口中的方法。采用注解方式如下：
        @Component          //扫描此类，作为bean加入IOC容器
        @Mapper             //mybatis注解，加在DAO层的接口上
        public interface UserDao {
        @Insert({"insert into users values(null,#{name},#{password},#{des})"})       //#{}：占位符‘？’  会从参数user对象中获取相应字段值？？ 
        public void InsertUser(User user);       //插入用户，用户对象为传入参数，返回受改变的行？？xxx error

        @Delete({"delete from users where name=#{name}"})
        public void DeleteUser(String name);

        @Update(value = {"update users set name=#{name}, password=#{password} where name=#{name}"})
        public void UpdateUser(User user);

        @Select({"select * from users"})
        public List<User> SelectUsers();       //查询所有用户，返回的是用户数组List
        }
        [xml文件的配置是否需要学？？？](https://mybatis.org/mybatis-3/zh/index.html)
     
  ##3.Service接口和其实现类ServerImpl的书写（@Autowired依赖注入DAO的实例，调用其方法操作数据库）
    ...省略
    
 ##4.Controller类的书写（匹配请求对应的方法，处理后返回model 和 view） 
   
     @Autowired依赖注入Service的实例，找到匹配请求地址的@RequestMapping的handler方法，调用ServiceImpl的方法，若有返回值则接收，将其放入model对象     （类似map，一个键一个值）中，return的String字符串为跳转的视图view.   
     @RequestParam：将请求中对应的值绑定到方法的参数中   
     /requestparam1?username=zhang            @RequestParam(value="username",required=false) String username  
     @PathVariable用于将请求URL中的模板变量映射到功能处理方法的参数上
     @RequestMapping(value="/users/{userId}/topics/{topicId}")    @PathVariable(value="userId") int userId, 
    填写表单数据发送到SpringMVC时，会将表单数据封装成对象，controller类中的那个处理方法的参数可以用对象来接收！！
    1、SpringMVC在处理表单提交时，可以把页面中的属性封装成对象；
    2、要求页面中各属性的name值要与封装成对象的属性值一一匹配；    // 表单中的name=“ ”的值要和封装的实体类的值相同
    3、在对象CLASS中，必须添加空的构造方法，否则在层级引入资源时会出现错误；
    
##5.thymeleaf的书写（HTML模板里加入（model里面存的）动态传过来的数据，通过model的key取出value）
        
        <tr   th:each="user : ${users}">     //取出键users对应的值，集合遍历（类似加强for循环）
            <th scope="row" th:text="${user.id}">1</th>      //取每个元素对应的属性值
            <td th:text="${user.name}">pancm</td>
            <td th:text="${user.password}">123456</td>
            <td th:text="${user.age}">16</td>
            <td><a th:href="@{/toEdit(id=${user.id})}">编辑</a></td>  //遍历寻找的时候也将对应id等所需的属性值放到地址后面
            <td><a th:href="@{/toDelete(id=${user.id})}">删除</a></td>
          </tr>
    再就是表单提交的书写.....
