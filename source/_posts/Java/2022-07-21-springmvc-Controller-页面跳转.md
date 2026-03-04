---
title: springmvc Controller 页面跳转
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-21 23:06:25
subtitle:
tags:
  - SSM
categories:
  -	Java
---

-  **如果返回值为**ModelAndView**，在处理方法中，返回**null**时，默认跳转的视图名称为请求名。跳转结果会根据视图解析器来跳转。**

```java
@RequestMapping("/hello.do")
    public ModelAndView hello(){
        System.out.println("hello================");
        return null;
    }
```

 **结果：** ![0](1.png)

-  **如果返回值为**ModelAndView,**在处理方法中，指定视图名称，那么将跳转到指定的视图名。跳转结果会根据视图解析器来跳转。**-----<font face = "黑体" color = green>**使用最多**</font>

```java
@RequestMapping("/hello.do")
    public ModelAndView hello(){
        System.out.println("hello================");
        return new ModelAndView("index");
    }
```

**结果：**![0](2.png)

- **返回值为**void**，在处理方法中，默认跳转的视图名称为请求名。跳转结果会根据视图解析器来跳转。**

```java
@RequestMapping("/hello.do")
    public void hello(){
        System.out.println("hello================");
}
```

**结果：** ![](3.png)

- **返回值为**void,**在处理方法中通过**ServletAPI**来进行跳转：**---**<font color=green>不用视图解析器</font>**

```java
 @RequestMapping("/hello.do")
    public void hello(HttpServletRequest req,HttpServletResponse resp) throws Exception{
        System.out.println("hello================");
        req.getRequestDispatcher("hello.jsp").forward(req, resp);
    }
```

**结果：** ![0](4.png)

- **返回值为**String,默认情况下，将会以返回值为视图名通过视图解析器来找到跳转的页面。

 ```java
 @RequestMapping("/hello.do")
     public String hello() {
         System.out.println("hello================");
         return "index";
     }
 ```

**结果：** ![0](5.png))

- 返回值为**String**，在处理方法中，返回**null**时，默认跳转的视图名称为请求名。跳转结果会根据视图解析器来跳转。

```java
@RequestMapping("/hello.do")
    public String hello() {
        System.out.println("hello================");
        return null;
    }
```

**结果**： ![0](6.png)

- 返回值为**String,**为返回值加上前缀**”redirect:”或者”forward:”那么将会根据返回值去进行转发或重定向，**<font color = green>不使用视图解析器：</font>

```java
@RequestMapping("/hello.do")
    public String hello() {
        System.out.println("hello================");
        return "forward:/index.jsp";
    }
```

结果：![0](7.png)

