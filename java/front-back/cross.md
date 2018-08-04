# 跨域访问

## 跨域

跨域是指从一个域名的网页去请求另一个域名的资源。比如从[http://www.baidu.com/](http://www.baidu.com/) 页面去请求 [http://www.google.com](http://www.google.com) 的资源。跨域的严格一点的定义是：只要 协议，域名，端口有任何一个的不同，就被当作是跨域。

ajax中XMLHttpRequest不能发起跨域请求

跨域请求的例子

[http://www.123.com/index.html](http://www.123.com/index.html) 调用 [http://www.123.com/server.php](http://www.123.com/server.php) （非跨域）

[http://www.123.com/index.html](http://www.123.com/index.html) 调用 [http://www.456.com/server.php](http://www.456.com/server.php) （主域名不同:123/456，跨域）

[http://abc.123.com/index.html](http://abc.123.com/index.html) 调用 [http://def.123.com/server.php](http://def.123.com/server.php) （子域名不同:abc/def，跨域）

[http://www.123.com:8080/index.html](http://www.123.com:8080/index.html) 调用 [http://www.123.com:8081/server.php](http://www.123.com:8081/server.php) （端口不同:8080/8081，跨域）

[http://www.123.com/index.html](http://www.123.com/index.html) 调用 [https://www.123.com/server.php](https://www.123.com/server.php) （协议不同:http/https，跨域）

## ajax解决跨域请求

通过设置crossDomain和xhrFields解决跨域请求（带cookie）

```text
$("#loginBtn").click(function(){
        $.ajax({
            type: "get",
            url: "http://192.168.43.142:8080/travelnotes/login",
            data: {
                "account": $("#account").val(),
                "password": $("#password").val()
            },
            //crossDomain和xhrFields跨域请求（带cookie）
            crossDomain: true,
            xhrFields: {
                   withCredentials: true
            },
            datatype: "json",
            success: function(data){
                console.log(data);
            },
            error: function(){
                console.log("失败");
            }
        });
    });
```

## Java代码解决跨域请求

```text
String url = request.getHeader("Origin");
        System.out.println(url);
        if(!url.isEmpty()) {
            response.setHeader("Access-Control-Allow-Origin", url);
            response.setHeader("Access-Control-Allow-Credentials","true");
        }
```

## Options 预请求

如果ajax跨域请求设置了headers，会调用一次options请求获取服务器是否允许此域名的请求的，如果没有设置Access-Control-Allow-Headers就会报跨域错误。

```text
response.setHeader("Access-Control-Allow-Headers","Token");
```

## Springmvc对跨域请求的解决方式

Spirngmvc 4.2以后才支持跨域请求的解决。

首先，在web.xml中增加dispatchOptionsRequest的配置

```text
<init-param>
      <param-name>dispatchOptionsRequest</param-name>  
      <param-value>true</param-value> 
</init-param>
```

```text
<servlet>
      <servlet-name>mvc-dispatcher</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springMVC.xml</param-value>
    </init-param>

    <init-param>
         <!-- Springmvc接受OPTIONS请求 
                开启后还需要响应的@CrossOrigin
         -->
      <param-name>dispatchOptionsRequest</param-name>  
      <param-value>true</param-value> 
     </init-param>

    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>mvc-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

然后在相应的方法上加注解[@CrossOrigin](http://blog.csdn.net/hj7jay/article/details/73613288)

```text
@CrossOrigin(allowedHeaders= "Token")
    @RequestMapping(value = "/login" , method = RequestMethod.POST)
    public void login(@RequestParam("account")String account,
            @RequestParam("password") String password, 
            HttpServletResponse response) {
        System.out.println("acount ="+account);
        System.out.println("password ="+password);

        String json = userService.login(account, password);
        System.out.println(json);
        try {
            response.setCharacterEncoding("UTF-8");
            response.getWriter().write(json);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

