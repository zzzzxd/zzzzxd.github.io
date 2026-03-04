---
title: ' @requestParam与@Param'
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-21 23:30:17
subtitle:
tags:
  - SSM
categories:
  - Java
---

**@RequestParam** 用于controller层，是Spring的注解

解决前台参数名称与后台接收参数变量名称不一致的问题，等价于request.getParam

- value：参数名字，即入参的请求参数名字，如username表示请求的参数区中的name为username的参数的值将传入；
- required：是否必须，默认是true，表示请求中一定要有相应的参数，否则将报404错误码；
- defaultValue：默认值，表示如果请求中没有同名参数时的默认值，默认值可以是SpEL表达式，如`“#{systemProperties['java.vm.version']}”`

```java
@ResponseBody
    @RequestMapping("login")
    public String login(@RequestParam(value = "username") final String username,
                        @RequestParam(value = "password",required = false) final String password,
                        @RequestParam(value = "valcode",required = false) final String valcode) {
                        }                   

```



**@Param** 用于dao层，是mybatis中的注解

使得mapper.xml中的参数与后台的参数对应上，也增强了可读性

如果两者参数名一致得话，spring会自动进行封装，不一致的时候就需要手动去使其对应上。

即：用注解来简化xml配置的时候,@Param注解的作用是给参数命名,参数命名后就能根据名字得到参数值,正确的将参数传入sql语句中 。

```java
public interface Mapper { 
   
@Select("select s_id id,s_name name,class_id classid"+ 
        "from student where  s_name= #{aaaa} and class_id = #{bbbb}") 
 public Student select(@Param("aaaa") String name,@Param("bbbb")int class_id);  
   
@Delete...... 

@Insert...... 
    
} 
```

在dao层，用来给参数命名，在Mybatis的mapper中加上该注解，传递的参数与Sql中的字段名一致

```java
 List<Employee>getAllEmployeeByPage(@Param("page") Integer page,
 					 @Param("size") Integer size);
```

