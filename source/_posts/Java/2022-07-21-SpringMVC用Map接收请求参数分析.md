---
title: SpringMVC用Map接收请求参数分析
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-07-21 23:48:32
subtitle:
tags:
  -	SSM
categories:
  -	Java
---

### **第一种情况**，什么也不设置，无参数传递

注解为 @Controller @RequestMapping

![img](1.png)

可以看到传递的为SpringMVC的BindingAwareModelMap类型，SpringMVC中的隐含模型就是这个类型，其作用域等价于 request 域，当添加Model、ModelMap参数时，SpringMVC实际传入的就是这个隐含模型；向这个隐含模型种设置值后，在返回的页面中就能通过request域取值。

### **第二种情况**，加个参数试试 => .../testmap?test1=2342

结果类型还是一样，且参数不会被传入，当然使用request肯定能取出来。

![img](2.png) 

### **第三种情况**，给Map参数添加@RequestParam注解

　　1、Get请求 => http://localhost:8080/ssm/v2/testmap?test1=234234

　　成功传入了参数，注意这个Map类型为LinkedHashMap，而不是隐含模型了

![img](3.png)

　　再添加个Model参数看看，隐含模型中依然没有值

　　所以添加@RequestParam注解后，SpringMVC会将 Get 请求中封装进对应的参数中，如果参数是Map就封装称LinkedHashMap而不再传入隐含模型

![img](4.png)

　　2、Post请求， 再测试测试Post请求

　　与Get的结果一致：参数无@RequestParam注解时，Map接收隐含模型；添加@RequestParam注解时，Map接收LinkedHashMap；隐含模型中无值。

![img](5.png)

### **第四种情况**，给Map参数添加@RequestBody注解，且请求方式为Post

出乎意料的也成功传入了，与@RequestParam注解结果类似，也是LinkedHashMap

![img](6.png)

![img](7.png)

复杂点的Json数据也能解析接收成功

![img](8.png) ![img](9.png) 

### **总结**

- SpringMVC处理请求用Map类型接收参数时，如果参数无注解，则会传入BindingAwareModelMap类型，等价于Model、ModelMap参数；

- 参数添加@RequestParam注解时，会将参数包装称LinkedHashMap对象，参数的key为Map的key，参数值为Map的key，支持Get、Post方法（应该支持Put、Delete，没有测，俩方法与Post类似）；

- 添加@RequestBody注解时，接收Json类型数据，也会包装成LinkedHashMap对象，该注解不支持Get请求，Get请求没有请求体不能传Json。
