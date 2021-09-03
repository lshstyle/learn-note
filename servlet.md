# Servelet定义

​       Serverlet Server Applet 的简称，称为服务连接器。是JavaWeb服务器端的程序，一般一个Servlet处理一种特定的请求。Servlet编写好后，需要制定其所处理的请求的请求路径，也可以认为Servlet是一种虚拟资源，可悲客户端请求。

​       Servlet接口被定义用来处理客户端发来的请求，又针对HTTP协议提供了子类HttpServlet处理HTTP请求。也就是说Servlet在顶层设计上可以处理各种协议的请求，只不过目前仅仅应用在HTTP请求上。

​      由于HTTP协议中请求方式主要有get和post，HttpServlet在就定义了doGet()和doPost()分别处理这两种方式的请求。不过一般的业务逻辑使用get或者post都可以，而且处理方式一样，所以一般都在一个方法中调用另一个方法，比如在doPost()中调用doGet();



# Servlet工作原理

![](.\img\221227143550.png)



# 虚拟路径

​       web 3.0版本之后，可以使用@WebServlet注解指定Servlet的虚拟路径，比如@WebServlet("/test"), 注意必须以斜线开头。项目启动时会扫描所有的@WebServlet注解并管理起来，可以为Servlet指定多个虚拟路径，比如@WebServlet({"/teset", "/test1", "/test2"})

​	  虚拟路径支持通配符， 比如/test/*,但是不能是/test\*  、 /\*.do 等。

​	特别的可以使用单个/匹配所有请求



# Servlet对象的创建时机

​	Servlet对象默认会在第一次被请求时创建，也可以通过配置让服务器启动时就创建Servlet对象

```
@WebServlet(urlPatterns = "/test", loadOnStartup = 1)
public class TestServlet extends HttpServlet {

    @Override
    public void init() throws ServletException {
        System.out.println("Servlet进行初始化");
}
```

   当指定loadOnStartup时，Servlet对象会在服务器启动时创建，取值为自然数，而且值越小优先级越高



# Request请求对象

 HttpServletRequest request是请求对象，内部封装了客户端请求的各种信息，主要有请求路径、请求参数、请求头、cookie等

| **request的方法**  | **说明**                                             |
| ------------------ | ---------------------------------------------------- |
| getRequestURI()    | 获得请求路径及其后面的查询字符串（不包括主机和端口） |
| getParameter(name) | 获得以键值对形式提交的请求参数的值                   |
| getHeader(name)    | 获得请求头信息                                       |
| getPart(name)      | 获得上传的文件                                       |
| getCookies()       | 获得随请求上传的cookie信息                           |

# Response响应对象

HttpServletResponse response是响应对象。

| **response的方法**     | **说明**                           |
| ---------------------- | ---------------------------------- |
| setContentType(type)   | 设置响应类型                       |
| getWriter()            | 获得Writer以便生成文本响应         |
| getOutputStream()      | 以便生成字节响应，比如文件下载等   |
| sendRedirect(location) | 直接生成重定向响应                 |
| addCookie(cookie)      | 在响应中添加cookie以便发送给客户端 |

# \<form>表单中请求路径的写法

\<form>表单是显示在客户端的浏览器上，\<form>表单是客户端的东西，不属于服务器。

## 相对路径(不以斜杠开头的路径)

相对路径是相对于当前浏览器地址栏路径来说的，即在地址栏父路径的基础上拼接相对路径，比如地址栏中路径为http://localhost:8080/webDemo/register.html,那么\<form  action="register"> 中的相对路径register就表示http://localhost:8080/webDemo/register,他们的父路径相同

## 绝对路径(以斜杠开头的路径)

绝对路径开头的斜线表示根路径

对于\<form action="/webDemo/register"> ，根路径表示服务器地址，即http://localhost:8080/,所以后面需要跟上项目名称webDemo

当然如果直接\<form action="http://localhost:8080/webDemo/register"> 写全路径也可以

# 转发和重定向

转发发生在客户端 forward，可以达到重用request中现有数据的效果

重定向即response.sendRedirect("/userDemo/login.jsp")， 生成的响应信息为

```
HTTP/1.1 302 Found

Location: /userDemo/login.jsp
```

浏览器收到这个响应后就会重新访问Location指定的URL,所以URL是在客户端生效，所以斜杠后面需要加上项目名称

重定向时会在其发送新的请求，这样就有2个请求，对于服务器来说，第一个request里面的数据就不能再用了，重定向还有个效果就是会修改浏览器地址栏的URL。

一般情况下，如果需要使用当前request中得数据就是用转发，如果想修改地址栏URL就使用重定向。

如果即想使用当前request数据，又想修改地址栏URL,可以使用URL重写技术，也就是把request中的数据取出来拼接到URL后面，这样重定向时浏览器再次请求时就会以请求参数的方式携带这些数据

# cookie

cookie技术可以把一些键值对数据放在客户端电脑上，方便实现一些功能效果。

cookie生效过程：

1.服务器收到请求后，可以创建cookie信息，然后把cookie以响应头的方式发送给浏览器，浏览器收到响应后，就会把响应头中的cookie信息保存到本地文件中。

```
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    Cookie cookie = new Cookie("email", "a@xxx.com");
　　 cookie.setMaxAge(60 * 60 * 24 * 7);//设置最大存活时间，单位秒
    response.addCookie(cookie);
}
```

maxAge设置cookie在客户端存活时间，单位秒，默认一级设置负值表示存活到浏览器关闭，0表示立即删除

响应信息

```
HTTP/1.1 200 OK

Set-Cookie: email="a@xxx.com"; Version=1; Max-Age=604800; Expires=Wed, 27-Sep-2017 07:01:02 GMT
```

2.浏览器再次发送请求时，就会把本地文件中存储的cookie信息放入请求头中发送回服务器，服务器就可以取出请求头中的cookie做一些事情。

请求信息

```
GET /webDemo/processCookie HTTP/1.1

Cookie: email="a@xxx.com"
```

```
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    Cookie[] cookies = request.getCookies();
    if (cookies != null) {
        for (Cookie cookie : cookies) {
            System.out.println(cookie.getName() + " : " + cookie.getValue());
        }
    }
}
```

# session

session表示会话，表示客户端和服务器的一次连接，即从用户打开浏览器访问网站开始，到用户关闭浏览器为止，就是一个session,期间所有对该网站的访问都属于同一个session

```
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    String name = request.getParameter("name");

    HttpSession session = request.getSession();
    session.setAttribute("name", name);
}
```

session在Servlet中表现为HttpSession的对象，session对象不应该手动创建，某用户第一次调用request.getSession()时，服务器会创建session对象，之后再次调用时，就会返回一个session对象。

session对象主要是作为web域对象来使用，应该存放那些在多个请求之间都有效的数据，比如用户登录成功后把user对象存放到session中，这样后面请求时，servlet就可以从session中取出user的数据直接使用。

session中还有一个比较重要的是sessionId，每个会话都会对应一个session对象，每个session对象都会拥有一个唯一的sessionId，这个sessionId就是用来区分不同用户会话。

session的实现借助了cookie技术，当创建session对象后，服务器会把session对象保存到一个map中，并且会把sessionId以cookie的形式发送到客户端，这样后面的请求都会携带这个sessionId和cookie。服务器就会根据cookie里面的sessionId到map中取到对应的session对象

创建session时的响应信息

```
HTTP/1.1 200 OK

Set-Cookie: JSESSIONID=74D7E93D031B34EDE9FD0D8EC652140D; Path=/webDemo/; HttpOnly
```

以后再请求时的请求信息

```
GET /webDemo/session HTTP/1.1

Cookie: JSESSIONID=74D7E93D031B34EDE9FD0D8EC652140D; email="a@xxx.com"
```

默认session对象的最大空闲时间是30分钟，也就是说用户如果30分钟内没有再次访问网站，服务器就会销毁该用户对应的session对象，如果30分钟后再次访问时，服务器会创建新的session,新的sessionId cookie也会覆盖原来的cookie

# ServletContext

ServletContext是Servlet的上下文对象，管理者Servlet所处的环境，其实代表整个web项目

| getContextPath()               | 返回项目名称（项目的虚拟路径），比如 /webDemo                |
| ------------------------------ | ------------------------------------------------------------ |
| getMimeType("a.txt")           | 根据后缀名确定文件的MIME类型，比如text/plain                 |
| getResourceAsStream("/el.jsp") | 返回项目内部资源的读取流                                     |
| getRealPath("/el.jsp")         | 获取项目内部资源文件的真实全路径，比如D:\apache-tomcat-8.0.36\wtpwebapps\webDemo\el.jsp |

​	另外ServletContext还作为web域对象，存储那些生命周期在服务器工作期间都有效的数据，但尽量不要把数据存放到该域对象中，因为如果用户量很多同时访问里面的数据时就会严重降低服务器性能

# Servlet的线程安全问题

每个Servlet只会创建一个对象，servlet对象可以同时处理多个请求，理论上Servlet的数据字段存在线程安全问题，但是进行线程同步会大大降低处理效率，所以为了避免线程安全问题并且不降低处理效率，就不能也不应该在Servlet内定义数据字段。