# SpringMVC

### 简介

springMVC是一个web层的mvc框架；

model：模型    

view：视图

controller：控制器



这是一种设计模式，将责任进行拆分，不同的组件复制不同的事情。

优点：

- 结构清晰
- 更好维护

缺点：

- 更加复杂



## 入门

### SpringMVC

流程图：

![1554862467901](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554862467901.png)

### 基本流程

1. 创建web项目
2. 编写web.xml，在其中注册一个特殊的servlet，前端控制器
3. 编写一个springMVC的配置文件
   1. 注册一个视图解析器
4. 编写一个控制器
5. 编写一个结果页面

(不要忘了导入依赖)

1. 创建项目：

![1554862745658](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554862745658.png)

2. web.xml 注册前端控制器

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
		 http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    <!--注册一个前端控制器  DispatcherServlet-->
    <servlet>
      <!--如果不去修改spring配置文件的默认位置
      那么springMVC会去web-inf下面去找一个叫做springmvc-servlet.xml的文件-->
      <servlet-name>SpringMVC</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    </servlet>
  
  <servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <!--这里统一写/-->
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```

3. 编写SpringMVC的配置文件——SpringMVC-servlet.xml

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置一个视图解析器-->
    <!--常用内部资源视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--1. 前缀-->
        <property name="prefix" value="/jsp/"/>
        <!--2. 后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>


    <bean class="com.shu.controller.HelloController" name="/helloController">

    </bean>
</beans>
```

4. 控制器Controller

``` java
package com.shu.controller;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

//实现
public class HelloController implements Controller {


    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("girl","小明");
        mav.setViewName("girl");
        return mav;
    }
}
```

5. 视图——结果界面

``` jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Girl</title>
</head>
<body>
    美眉: ${girl}
</body>
</html>
```

### 组件分析



1. web.xml

   注册前端控制器，目的在于，我们希望让springmvc去处理所有的请求

   通过相关映射配置

   ```xml
     <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <!--这里统一写/-->
       <url-pattern>/</url-pattern>
     </servlet-mapping>
   ```

   确实是能处理所有的请求(但并不是真的所有)

   <url-pattern>的写法：

   1. /     （处理所有的请求，处理完后不会再去将girl.jsp当作一个新的请求，将这个渲染的结果直接返回浏览器）
   2. /*   (不能这么写)
   3. *.do （增加尾部的do来实现对行为的区分）

2. 前端控制器

   SpringMVC的设计理念是希望开发者远离原生的Servlet API，将原先的Servlet的操作进一步简化，它将很多东西责任进行了拆分，能够做到随意的切换，其本身还是通过servlet设计的。

3. SpringMVC的配置文件

   一般情况下，配置文件的名称要与定义的DispatcherServlet的<servlet-name>相关，位置在WEB-INF的文件夹下，其命名格式：

   1. [servletName]-servlet.xml

   2. [servletName]-servlet=namespace

      ![1554881821896](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554881821896.png)

   如果想要在其他的文件夹下实现对DispatcherServlet的声明，则可以通过配置上下文位置的参数实现

   ![1554882013936](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554882013936.png)

4. 视图解析器

   springMVC支持多种视图技术

   - jsp
   - freemaker（模板技术）

   内部的资源视图解析器

   - 视图前缀

     /jsp/  这是请求响应的资源的路径的配置.   viewName:girl    /jsp/girl

   - 视图后缀

     - jsp  此时前缀+视图名称+后缀=/jsp/girl.jsp

   因此逻辑视图View其实在SpriingMVC中就主要包括了3部分——prefix+logicViewName+suffix

   其效果与request.getDS.forward是一样的

5. 控制器——Controller

   Controller是由一种比较传统的实现一种接口的方式完成的。

   ``` java
   package org.springframework.web.servlet.mvc;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   import org.springframework.lang.Nullable;
   import org.springframework.web.servlet.ModelAndView;
   
   @FunctionalInterface
   public interface Controller {
       @Nullable
       ModelAndView handleRequest(HttpServletRequest var1, HttpServletResponse var2) throws Exception;
   }
   ```

   其接口内部只有一个方法，这种接口就被称作为函数式接口。其实质就是servlet中doGet、doPost的响应方法，其入参也就是请求与响应。

   Controller 中将其设计为ModelAndView，在model中填充数据，然后在具体的视图上进行展示。

   具体的实现还需要再配置文件中配置这个bean，需要取名来充当这个请求的url。

   但其实质上就处理了一个请求，与servlet的差别不大。

## 注解开发模式

​	相比较之下，采取注解开发相对来说更简单。

## 基本注解

- @Controller

  当对一个类添加Controller的注解后，就标记其为Spring的一个组件，并且是控制器的组件，此时handlermapping就会去扫描这个Controller是否与之匹配，如果发现匹配就把处理的工作交给它。

- @RequestMapping

  其中添加指向的url，比较好的习惯是在类的头添加概括性的url注释 eg."/order"  ，然后根据具体方法内部实现功能的不同再方法上添加更为详细的的注解 eg."/addOrder"......

### 开发步骤

1. 配置基础扫描的包，这样配置的注解才会生效。(注意相关头文件的写入——Context)

   ``` xml
    <!--配置一个注解扫描的包-->
    <context:component-scan base-package="com.shu.controller"/>
   ```

2. 在指定的类上面添加@Controller注释。

3. 添加@RequestMapping中的地址指向，类似于在Controller的bean中配置的Name。具体方法中返回的String就是视图的名称。

   ``` java
   package com.shu.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   public class ModelController {
   
   
       //ModelAndView Model + View
       @RequestMapping("/Model")
       public String bye(Model model){
           model.addAttribute("Model","摩登");
           //这里的return的就是ViewName
           //此时取得就是/jsp/model.jsp页面
           return "model";
       }
   }
   ```



### 遍历实验

``` java
package com.shu.controller;

import com.shu.pojo.Orders;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/orders")
public class ModelController {

    @RequestMapping("/addOrder")
    public String addOrder(Model model){
        List<Orders> list = new ArrayList<>();
        Orders o = new Orders();
        o.setId(1);
        o.setName("psp");
        Orders o2 = new Orders();
        o2.setId(2);
        o2.setName("ps4");

        list.add(o);
        list.add(o2);

        model.addAttribute("orders",list);

        return  "orders";

    }

}
```

``` jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%--
  Created by IntelliJ IDEA.
  User: 12084
  Date: 2019/4/10
  Time: 16:48
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>订单</title>
</head>
<body>

    <c:forEach items="${orders}" var="obj">
        <tr>
            <td>${obj.id}</td>
            <td>${obj.name}</td>
        </tr>
    </c:forEach>

</body>
</html>
```



## 常用注解

### 转发与重定向

1. 转发到页面：默认的选项
2. 重定向到页面： redirect：path
3. 转发到另外一个控制器： forward：path

### 关于SpringMVC访问Web元素的问题

可以直接通过模拟的对象完成操作，也可以使用原生的ServletAPI完成，直接在方法当中完成入参即可。

1. 模拟request请求，与传入真实的HttpServletRequest请求(继承自ServletRequest，相比较起来更适用于Http属性)：

![1554949428088](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554949428088.png)

2. session与其中的getServletContext

![1554949697974](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554949697974.png)

3. application

## 注解详解

### @requestMapping

- value： 写的是路径，是一个数组的形式，因此请求映射的路径path可以写多个值，实现对多个路径的匹配。

- path： value的别名，二者任选其一，作用是一样的。

- method：在不限定的情况下，所有的请求都可以通过，如果限定之后只允许对应的请求通过。

  eg. method = RequestMethod.GET	这里只允许GET方法通过

- params：可以指定参数，还可以去限定这个参数的特征，必须等于某个值或者不等于某个值。

- headers：限定头文件相关的信息，能够影响浏览器的相关行为。

- conmusers：消费者，媒体类型。eg.可以限定必须为application/json; charset=UTF-8

- produces：生产者，产生的响应的类型。

### 关于请求路径

springMVC支持ant风格。

1.  ？  任意的字符，斜杠例外

2. ``` java
   * 0到n，任意个字符都可以 不能含有斜杠  eg. /m3dgarhad=/m3asgfdasgh
   ```

3. ``` java
   /** 支持任意层的路径  **相当于*  eg. /m3/3sdfs/fdbf/ndg=/m3
   ```

4. ``` java
   /*  支持单层的任意个字符   eg. /m3/asfdsaf=/m3/safasf
   ```

### @GetMapping、@PostMapping

与requestMapping效果相同，但方法限定为了get与post。

### 对于非Get Post请求的支持

对于非get post请求的支持，需要有额外的内容添加，要增加一个过滤器来额外处理。

- 过滤器
- 返回的不是页面而是数据
- 表单提交中还要添加一个隐藏的参数 其中name="_method" value="DELETE"

### 关于静态资源的访问

由于servlet中设置了URL匹配方法为/，所以他将静态资源也当作一个后台的请求，并尝试去匹配，比如：

![1554964829595](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554964829595.png)

尝试去匹配static/css/index.css的Controller中的RequestMapping的组合，因为没有，所以404.

解决方式1，让SpringMVC单独处理，将这个交给容器的默认Servlet处理，而不是DispatcherServlet。

```xml
   <!--默认的servlet处理-->
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven/>
    <!--必须添加第二行，否则相当于Controller都由默认的servlet处理-->
```

解决方式2，通过映射关系描述一一去书写规则。

![1554965427293](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554965427293.png)

解决方式3，自行在web.xml中定义映射规则。

### @PathVariable

restful风格：

![1554966010159](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554966010159.png)

### @ResonseBody

用于返回数据，一般情况下返回JSON格式

### @ModelAttribute

在Controller里面的任意一个处理具体的方法之前都会执行。

使用方式一：

![1554969684152](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554969684152.png)

如果某些对象从头到尾每次请求当中都要存在，不消失的时候就可以使用

使用方式二：

![1554969852391](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554969852391.png)

使用方式三：

如果没有传递模型过来，那么方法上加了@ModelAttribute的会直接提供，如果有传递过来的模型则用传递过来的。

![1554970126837](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554970126837.png)

### @RequestBody

json数据，不是通过form表单传递

ajax({

​		data:

})

### @InitBinder



### @SessionAttributes

用在类上面，他会将模型自动添加到会话上面去。

### @SessionAttribute

要求当前这场访问的会话当中必须要有某个对象

###  @CookieValue

![1554972517579](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554972517579.png)















## 附

1. JSTL标准标签库

   <http://www.runoob.com/jsp/jsp-jstl.html>

2. 通过重写init的方法，在生命周期开始就于整体应用的上下文当中存取一个描述上下文路径的全文变量。这里存储了一个名为ctx的变量。

   ``` java
   package com.shu.Servlet;
   
   import javax.servlet.ServletConfig;
   import javax.servlet.ServletException;
   import javax.servlet.annotation.WebServlet;
   import javax.servlet.http.HttpServlet;
   
   @WebServlet
   public class WebServletInitServlet extends HttpServlet {
   
       @Override
       public void init(ServletConfig config) throws ServletException {
           config.getServletContext().setAttribute("ctx",config.getServletContext().getContextPath());
           super.init(config);
       }
   }
   ```

3. 关于post请求中文乱码问题解决

   Servlet中添加一个过滤器可，SpringMVC提供了一个非常好的字符编码过滤器，注册即可。

   ``` xml
       <filter>
           <filter-name>CharacterEncodingFilter</filter-name>
           <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
           <init-param>
               <param-name>encoding</param-name>
               <param-value>UTF-8</param-value>
           </init-param>
           <init-param>
               <param-name>forceRequestEncoding</param-name>
               <param-value>true</param-value>
           </init-param>
       </filter>
       <!--指定字符编码-->
       <filter-mapping>
           <filter-name>CharacterEncodingFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
   ```

4. form表单提交数据的方式

   1. 通过属性名字绑定：

      通过属性名称进行绑定，可以完成数据传递。

      页面当中表单元素的name值，要和后台的形参的名字保持一致。

      如果有多个形参，按名字绑定即可。

      ![1554968341805](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554968341805.png)

      ![1554968363157](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554968363157.png)

   2. 利用@RequestParam

      这样的含义更加明确。

      ![1554968448654](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554968448654.png)

   3. 使用pojo形式进行传递

      创建一个含有需要属性值的pojo，在内部进行传递

      ![1554968753358](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554968753358.png)

   4. 官方文档中日期格式的转换，通过确定输入的日期格式，改变系统接收的日期格式。

      ![1554969235429](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554969235429.png)

      或者通过注解在日期属性中指定

      ![1554971085739](C:\Users\12084\AppData\Roaming\Typora\typora-user-images\1554971085739.png)

   5. 