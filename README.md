# primary-ssm-web
###0.项目概览 

   利用IDEA（或STS）构建Maven项目pom.xml导入dependency，spring-boot-starter-thymeleaf（thymeleaf），spring-boot-starter-web（Spring MVC）
   mybatis-spring-boot-starter（Mybatis），mysql-connector-java（MySQL 驱动）。
   
###1.Navicat建库建表（创建数据库“schooltest”，包含表“stu”，里面有字段“stu_id,stu_name,stu_password,stu_des”）
     
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
    
    
    
