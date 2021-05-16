# Servlet

### Servlet的产生背景

随着Intenet和浏览器的发展，C/S架构（Client/Server）由于维护和升级成本高，于是出现了B/S架构（Browser/Server），早期B/S架构浏览器请求的都是**静态资源**，随着网络规模的发展，请求**动态资源**成为人们的主要诉求。

静态资源：Web页面中供人们浏览的数据始终不变
动态资源：Web页面中供人们浏览的数据由程序产生，不同时间点访问页	面看到的内容各不相同

Java有两种方案来实现动态需求，它们都属于JavaEE技术的一部分

- Applet
    - 浏览器通过Applet（Java插件）解释执行Web服务器发过来的Java代码，从而可以实现动态，但是，显然这种方案不好，既需要浏览器必须安装插件，又受限于浏览器，所以Java代码不能太多和太复杂。
- Servlet（Server Applet）
    - 既然浏览器不方便执行Java代码，那自然还是由服务器来执行了，所以Servlet出现了，Servlet就是Server端的Applet的意思，换句话说，Servlet就是运行在服务端的Java类。

这种使用Java语言进行动态Web资源开发的技术统称为**JavaWeb**。

### Servlet规范

当服务器收到浏览器发送的HTTP请求时，需要调用服务端程序（Java类）来处理，一般不同的请求需要调用不同的Java类，那么问题来了，HTTP怎么知道应该调用哪个Java类呢？最直接的办法就是在HTTP服务器代码里写一堆if else逻辑判断，这样HTTP服务器的代码跟业务逻辑耦合在一起了。

于是发明了Servlet容器，Servlet 容器用来加载和管理业务类。HTTP服务器不直接跟业务类打交道，而是把请求交给Servlet容器去处理，Servlet容器会将请求转发到具体的Servlet，如果这个Servlet还没创建，就加载并实例化这个Servlet，然后调用这个Servlet的方法。

但是每个业务类都是自定义的，应该调用哪个方法呢？于是定义了Servlet接口，各种业务类都必须实现这个接口，实现了该接口的业务类叫做Servlet。

而Servlet接口和Servlet容器这一整套规范叫作Servlet规范。

![1](../../../../assets/Servlet/1.png)

### Servlet接口

Servlet接口定义了下面五个方法：

```java
public interface Servlet {
    void init(ServletConfig config) throws ServletException;

    ServletConfig getServletConfig();

		// 具体业务类在这个方法里实现处理逻辑
    void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;

    String getServletInfo();

    void destroy();
}
```

### Servlet接口的抽象类

- GenericServlet
用于定义一个通用的、与协议无关的Servlet
- HttpServlet
用于创建适用于网站的HTTP Servlet

### Servlet 的生命周期

- Servlet 容器在加载 Servlet 类的时候会调用 init 方法，每次请求都会调用service方法，在卸载的时候会调用 destroy 方法。

### ServletRequest接口

- 提供客户端请求信息，可以从中获取到任何请求信息。
- Servlet容器创建一个ServletRequest对象，并将其作为参数传递给Servlet的service方法。

### HttpServletRequest接口

- 提供HTTP请求信息的功能。
- 不同于表单数据，在发送HTTP请求时，HTTP请求头直接由浏览器设置。
- 可直接通过HttpServletRequest对象提供的一系列get方法获取请求头数据
- 接收中文乱码
    - 原因：浏览器在提交表单时，会对中文参数值进行自动编码。当Tomcat服务器接收到浏览器请求后自动解码，当编码与解码方式不一致时,就会导致乱码。
    - GET

        ```java
        将接收到的中文乱码重新编码: 
        		// 接收到get请求的中文字符串 
        		String name = request.getParameter("name"); 
        		// 将中文字符重新编码，默认编码为ISO-8859-1 
        		String userName = new String(name.getBytes(“ISO-8859-1”),“utf-8");
        ```

    - POST

        ```java
        接收之前设置编码方式： 
        		request.setCharacterEncoding(“utf-8”) 
        提示：
        		必须在调用request.getParameter(“name”)之前设置
        ```

### ServletResponse接口

- 向客户端发送响应。
- Servlet容器创建ServletResponse对象，并将其作为参数传递给servlet的service方法。

### HttpServletResponse接口

- javax.servlet.http.HttpServletResponse接口继承ServletResponse接口，以便在发送响应时提供特定于HTTP的功能。
- setContentType() 设置服务器和浏览器的编码方式，避免ISO-8859-1字符集不支持中文导致乱码

### ServletConfig接口

- 用于描述Servlet本身的相关配置信息，在初始化期间用于将信息传递给Servlet配置对象。
- 配置方式

    ```xml
    <!— 在web.xml中配置ServletConfig初始化参数 —>
    <servlet>
    		<servlet-name>actionservlet</servlet-name>
    		<servlet-class>com.lagou.demo01.ActionServlet</servlet-class>
    		<!— 配置 Serlvet 的初始化参数 —>
    		<init-param>
    		<!-- 参数名 -->
    				<param-name>config</param-name>
    				<!-- 参数值 --> 
    				<param-value>struts.xml</param-value>
    		</init-param>
    </servlet>
    ```

# Servlet容器

- 工作流程
    - 当客户请求某个资源时，HTTP 服务器会用一个 ServletRequest 对象把客户的请求信息封装起来，然后调用 Servlet 容器的 service 方法，Servlet 容器拿到请求后，根据请求的 URL 和 Servlet 的映射关系，找到相应的 Servlet，如果 Servlet 还没有被加载，就用反射机制创建这个 Servlet，并调用 Servlet 的 init 方法来完成初始化，接着调用 Servlet 的 service 方法来处理请求，把 ServletResponse 对象返回给 HTTP 服务器，HTTP 服务器会把响应发送给客户端。

        ![2](../../../../assets/Servlet/2.jpeg)

- Web应用
    - 目录结构
        - Servlet 容器会实例化和调用 Servlet，那 Servlet 是怎么注册到 Servlet 容器中的呢？一般来说，我们是以 Web 应用程序的方式来部署 Servlet 的，而根据 Servlet 规范，Web 应用程序有一定的目录结构，在这个目录下分别放置了 Servlet 的类文件、配置文件以及静态资源，Servlet 容器通过读取配置文件，就能找到并加载 Servlet。Web 应用的目录结构大概是下面这样的：

            ```xml
            | -  MyWebApp
                  | -  WEB-INF/web.xml        -- 配置文件，用来配置Servlet等
                  | -  WEB-INF/lib/           -- 存放Web应用所需各种JAR包
                  | -  WEB-INF/classes/       -- 存放你的应用类，比如Servlet类
                  | -  META-INF/              -- 目录存放工程的一些信息
            ```

    - ServletContext接口
        - javax.servlet.ServletContext接口主要用于定义一组方法，Servlet使用这些方法与它的Servlet容器通信。
        - 服务器容器在启动时会为每个项目创建唯一的一个ServletContext对象，用于实现多个Servlet之间的信息共享和通信，由于 ServletContext 持有所有 Servlet 实例，你还可以通过它来实现 Servlet 请求的转发。
        - 在Servlet中通过this.getServletContext()方法可以获得ServletContext对象。
        - 配置方式

            ```xml
            <!-- 在web.xml中配置ServletContext初始化参数 -->
            <context-param>
            		<param-name>username</param-name>
            		<param-value>scott</param-value>
            <context-param>
            
            <context-param>
            		<param-name>password</param-name>
            		<param-value>tiger</param-value>
            <context-param>
            ```

- 扩展机制
    - Filter
    - Listener

## Servlet的编程步骤

- 建立一个Java Web Application项目并配置Tomcat服务器。
- 自定义类实现Servlet接口或继承 HttpServlet类（推荐） 并重写service方法。
- 将自定义类的信息配置到 web.xml文件并启动项目，配置方式如下：

    ```xml
    <!-- 配置Servlet --> 
    <servlet> 
    		<!-- HelloServlet是Servlet类的别名 -->
    		 <servlet-name> HelloServlet </servlet-name> 
    		<!-- com.lagou.task01.HelloServlet是包含路径的真实的Servlet类名 --> 
    		<servlet-class> com.lagou.task01.HelloServlet </servlet-class> 
    </servlet> 

    <!-- 映射Servlet --> 
    <servlet-mapping> 
    		<!-- HelloServlet是Servlet类的别名，与上述名称必须相同 --> 
    		<servlet-name> HelloServlet </servlet-name> 
    		<!-- /hello是供浏览器使用的地址 --> 
    		<url-pattern> /hello </url-pattern> 
    </servlet-mapping>
    ```

- 在浏览器上访问的方式为：

    ```xml
    http://localhost:8080/工程路径/url-pattern的内容
    ```

## 转发

- 概念
    - 一个Web组件（Servlet/JSP）将未完成的处理通过容器转交给另外一个Web组件继续处理，转发的各个组件会共享Request和Response对象。
- 特点
    - 转发之后浏览器地址栏的URL不会发生改变。
    - 转发过程中共享Request对象。
    - 转发的URL不可以是其它项目工程。

## 重定向

- 概念
    - 首先客户浏览器发送http请求，当web服务器接受后发送302状态码响应及对应新的location给客户浏览器，客户浏览器发现是302响应，则自动再发送一个新的http请求，请求url是新的location地址，服务器根据此请求寻找资源并发送给客户。
- 特点
    - 重定向之后，浏览器地址栏的URL会发生改变。
    - 重定向过程中会将前面Request对象销毁，然后创建一个新的Request对象。
    - 重定向的URL可以是其它项目工程。

## **Servlet线程安全**

- 服务器在收到请求之后，会启动一个线程来进行相应的请求处理。
- 默认情况下，服务器为每个Servlet只创建一个对象实例。当多个请求访问同一个Servlet时，会有
- 多个线程访问同一个Servlet对象，此时就可能发生线程安全问题。
- 多线程并发逻辑，需要使用synchronized对代码加锁处理，但尽量避免使用。

## 状态管理

- Web程序基于HTTP协议通信，而HTTP协议是”无状态”的协议，一旦服务器响应完客户的请求之后，就断开连接，而同一个客户的下一次请求又会重新建立网络连接。

- 服务器程序有时是需要判断是否为同一个客户发出的请求，比如客户的多次选购商品。因此，有必要跟踪同一个客户发出的一系列请求。

- 把浏览器与服务器之间多次交互作为一个整体，将多次交互所涉及的数据保存下来，即状态管理。多次交互的数据状态可以在客户端保存，也可以在服务器端保存。状态管理主要分为以下两类：

  - 客户端管理：将状态保存在客户端。基于Cookie技术实现。
  - 服务器管理：将状态保存在服务器端。基于Session技术实现。

## Cookie技术

### 基本概念

- Cookie本意为”饼干“的含义，在这里表示客户端以“名-值”形式进行保存的一种技术。

- 浏览器向服务器发送请求时，服务器将数据以Set-Cookie消息头的方式响应给浏览器，然后浏览器会将这些数据以文本文件的方式保存起来。

- 当浏览器再次访问服务器时，会将这些数据以Cookie消息头的方式发送给服务器。

### Cookie的生命周期

- 默认情况下，浏览器会将Cookie信息保存在内存中，只要浏览器关闭，Cookie信息就会消失。
- 如果希望关闭浏览器后Cookie信息仍有效，可以通过Cookie类的成员方法实现。

### Cookie的路径问题

- 浏览器在访问服务器时，会比较Cookie的路径与请求路径是否匹配，只有匹配的Cookie才会发送给服务器。

- Cookie的默认路径等于添加这个Cookie信息时的组件路径，例如：/项目名/目录/add.do请求添加了一个Cookie信息，则该Cookie的路径是 /项目名/目录。
- 访问的请求地址必须符合Cookie的路径或者其子路径时，浏览器才会发送Cookie信息。

### Cookie的特点

- Cookie技术不适合存储所有数据，程序员只用于存储少量、非敏感信息，原因如下：
  - 将状态数据保存在浏览器端，不安全。
  - 保存数据量有限制，大约4KB左右。
  - 只能保存字符串信息。
  - 可以通过浏览器设置为禁止使用。

## Session技术

### 基本概念

- Session本意为"会话"的含义，是用来维护一个客户端和服务器关联的一种技术。

- 浏览器访问服务器时，服务器会为每一个浏览器都在服务器端的内存中分配一个空间，用于创建一个Session对象，该对象有一个id属性且该值唯一，我们称为SessionId，并且服务器会将这个SessionId以Cookie方式发送给浏览器存储。

- 浏览器再次访问服务器时会将SessionId发送给服务器，服务器可以依据SessionId查找相对应的Session对象

### Session的生命周期

- 为了节省服务器内存空间资源，服务器会将空闲时间过长的Session对象自动清除掉，服务器默认的超时限制一般是30分钟。

- 使用javax.servlet.http.HttpSession接口的成员方法实现失效实现的获取和设置。

- 可以配置web.xml文件修改失效时间。

  ```xml
  <session-config> 
      <session-timeout>30</session-timeout> 
  </session-config>
  ```

### Session的特点

- 数据比较安全。
- 能够保存的数据类型丰富，而Cookie只能保存字符串。
- 能够保存更多的数据，而Cookie大约保存4KB。
- 数据保存在服务器端会占用服务器的内存空间，如果存储信息过多、用户量过大，会严重影响服务器的性能。

## 与其他实现对比

- CGI（Common Gateway Interface）程序使用C、Shell Script或Perl编写，CGI是为特定操作系统编写的（如UNIX或Windows），不可移植，CGI程序对每个请求产生新的进程去处理。

[Servlet的历史和规范](https://blog.csdn.net/u010297957/article/details/51498018)