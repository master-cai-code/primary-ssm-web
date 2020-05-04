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
     
     ###
     where  子句在group by 之前筛选，不能跟聚集函数
     having 则在group by 之后，进行分组后，选出满足条件的组
     
     ### 
     外键foreign key 是从表里的键是主表的主键，起到连接两表的作用
     修改从表是拒绝的，而修改主表可以级联删除或修改从表              （小弟死了不会影响大哥，但大哥死了小弟会受牵连）
     
     ###
     全相连：并集（mysql没有full outer join 关键字，使用union关键字合并左连接和右连接），找到显示，没匹配就是null
     左连接：以左边的为基准，去查找右表的数据，找到就显示，没找到就为null
     右连接：以右边的为基准...
     内连接：交集
     
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

  2020.3.17---16:25
## 关于一些补充和以前疑惑的解答：

     ##Spring MVC的大概流程：
     1.请求来到dispatcherServlet（以下简称DS）
     2.DS根据请求（URL地址）查找匹配的handler（处理请求的具体方法）----中间经过handlerMapping和handlerAdapter
     3.处理方法会生成ModelAndView对象，可拆解成Model（数据）和View（逻辑视图，也就是html页面的名字，需要进行前缀后缀拼接等）
     4.Model把数据传给对应的html页面，完成响应
     --------------------------和之前想法差不多
       

    ##Spring的IOC和AOP原理：
    1.IOC控制反转：原来对象是程序员new出来的，现在是IOC创建对象，然后运行时注入到需要的实例当中（原来是自己做饭，现在是打电话叫外卖）松耦合
    2.IOC的原理流程：-----------java反射
      分为两步：IOC创建beans   和   给有依赖的实例注入beans
      2.1  spring容器获取 扫描包 下面所有的类
      2.2  获取各个类上的注解信息
      2.3  如果有注解，进行bean的实例化（beanId---类名小写，通过类名获得实例化对象）  -----这个仿源码的样例根据beanId名称来注入
      2.4  将该bean添加到beans的ConcurrentHashMap<String,Object>集合中（单例，只创建一次）
      
      2.5  利用反射去查找类中的属性上是否有注解
      2.6  根据该属性名称在Map集合中找到对应的bean注入（这个样例是 属性名和beanId要一模一样）  默认是根据类型注入，这个样例是根据名字注入
    3.AOP面向切面：  动态的增强一个方法，在其前面，后面添加新功能（在什么时候，什么地方，做什么增强）    将两个业务逻辑分离  也实现了松耦合
    4.AOP的原理流程：-----------动态代理
      4.1  动态代理的实现：           目标对象和代理对象
              1. 实现InvocationHandler接口，重写里面的invoke（）方法，    也要用一成员变量来接收目标对象：private Object target;
              2. 在invoke(）方法里进行“增强”   
                 //xxxx前置增强   
                 Object result =method.invoke(target, args); //原目标对象的旧方法   返回值=方法名.invoke（对象，参数）--> 对象.方法（参数） 
                // xxxx后置增强
              3. 任意写一个方法（如getProxy()）用于返回代理的对象
              4. 在方法里 调用Proxy.newProxyInstance（classloader，interfaces，InvocationHandler）  生成代理对象
              5. main方法里面使用代理对象调用原始接口里面的方法（就是去执行增强的invoke方法了）
          
          
    ##Mybatis的原理：  读取配置文件，SqlSessionFactory.openSession()，创建sqlSession,获取mapper接口，调用接口配置的方法，执行sql语句.......
    
    
    
    
 #关于参数的补充
 
  Spring MVC
  
    1.@RequestParam ： 一般用于接收get请求传过来的参数（直接跟在URL地址后面，如：localhost：8080/hello?name=zhang&age=18）用问号隔开，中间用&
    2.@PathVariable ： 用于接收URL中的直接参数（如：localhost：8080/hello/{name}）
    3.@RequestBody  ： 一般用于接收post请求表单之类的多个参数（请求体当中的参数），有JavaBean可以封装成对应的对象，再直接取出
     其余参数
         @Controller   @RequestMapping(@GetMapping,@PostMapping,@PutMapping,@DeleteMapping)
         利用model把执行结果参数传给html模板中
  
 Mybatis
    
    注解SQL用于简单语句，xml用于复杂语句操作，杂交编写
    1.xxxMapper.xml实际是Dao接口的实现类，namespace为类名，标签的id为方法名，ResultMap：返回类型，参数类型可以省略不写
    2.用#{}接收参数  ，#{xxx}
    3.对于一个参数的，#{}的xxx随便写，不过最好和参数名相同，有可读性
    4.两个或多个参数的，在Dao中用@Param（“指定名字”）绑定参数，SQL语句中再根据#{指定名字}取出参数的值
    5.多个参数，打包成JavaBean，再直接通过JavaBean的属性名取出属性的值
    6.多个参数也可以包装成Map集合，根据key取出value
    
    再就是获取自增主键 UseGenerateKey   keyProperty ......           动态SQL又待续........
   
   
