# OAuth

##### 名词定义：

Third-party application   第三方应用程序 又称"客户端"（client）

HTTP service  HTTP服务提供商 又称 “服务提供商”

Resource Owner 资源所有者  又称“用户”（user）

User Agent 用户代理  又称“浏览器”

Authorization server 认证服务器 授权服务器

Resource Server 资源服务器

##### 定义

OAuth引入了一个授权层，用来分离两种不同的角色：客户端和资源所有者。资源所有者同意后，资源服务器可以向客户端颁发令牌，客户端通过令牌，去请求数据。

## 1.授权码

##### step1

用户访问客户端，客户端跳转到认证服务器，认证服务器询问用户是否给予客户端授权。

```
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```

- response_type：表示授权类型，必选项，此处的值固定为"code" 
- client_id: 表示客户端的ID，必选项 
- redirect_uri : 表示申请的权限范围，可选项 
- scope：表示申请的权限范围，可选项
- state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值 

##### step2

用户给予授权

##### step3

认证服务器根据回调地址回传授权码

```
HTTP/1.1 302 Found
Location: https://client.example.com/cbcode=SplxlOBeZQQYbYS6WxIA
          &state=xyz
```

- grant_type：表示使用的授权模式，必选项，此处的值固定为"authorization_code"。
- code：表示上一步获得的授权码，必选项。
- redirect_uri：表示重定向URI，必选项，且必须与A步骤中的该参数值保持一致。
- client_id：表示客户端ID，必选项。

##### step4

客户端拿到授权码后，向认证服务器请求令牌

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

- grant_type：表示使用的授权模式，必选项，此处的值固定为"authorization_code"。
- code：表示上一步获得的授权码，必选项。
- redirect_uri：表示重定向URI，必选项，且必须与A步骤中的该参数值保持一致。
- client_id：表示客户端ID，必选项。

##### step5

获取token

```
     HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```

 

- access_token：表示访问令牌，必选项。
- token_type：表示令牌类型，该值大小写不敏感，必选项，可以是bearer类型或mac类型。
- expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
- refresh_token：表示更新令牌，用来获取下一次的访问令牌，可选项。
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。

## 2.隐藏式

不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，不需要授权码。

##### step1

客户端将用户导向认证服务器。

```
    GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz
        &redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
    Host: server.example.com
```

- response_type：表示授权类型，此处的值固定为"token"，必选项。
- client_id：表示客户端的ID，必选项。
- redirect_uri：表示重定向的URI，可选项。
- scope：表示权限范围，可选项。
- state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。

##### step2

用户决定是否给于客户端授权。

##### step3

假设用户给予授权，认证服务器将用户导向客户端指定的"重定向URI"，并在URI的Hash部分包含了访问令牌。

```
   HTTP/1.1 302 Found
     Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA
               &state=xyz&token_type=example&expires_in=3600
```

- access_token：表示访问令牌，必选项。
- token_type：表示令牌类型，该值大小写不敏感，必选项。
- expires_in：表示过期时间，单位为秒。如果省略该参数，必须其他方式设置过期时间。
- scope：表示权限范围，如果与客户端申请的范围一致，此项可省略。
- state：如果客户端的请求中包含这个参数，认证服务器的回应也必须一模一样包含这个参数。

##### step4

浏览器向资源服务器发出请求，其中不包括上一步收到的Hash值。

##### step5

资源服务器返回一个网页，其中包含的代码可以获取Hash值中的令牌。

##### step6

浏览器执行上一步获得的脚本，提取出令牌。

##### step7

浏览器将令牌发给客户端。

## 3.密码式

用户向客户端提供自己的用户名和密码，客户端使用这些信息，向“服务提供商”索要授权。

## step1

用户向客户端提供用户名和密码。

## step2

客户端将用户名和密码发给认证服务器，向后者请求令牌。

```
   POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=password&username=johndoe&password=A3ddj3w
```

- grant_type：表示授权类型，此处的值固定为"password"，必选项。
- username：表示用户名，必选项。
- password：表示用户的密码，必选项。
- scope：表示权限范围，可选项。

## step3

认证服务器确认无误后，向客户端提供访问令牌。

```
HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
       "example_parameter":"example_value"
     }
```

## 4.客户端模式

客户端模式是指客户端以自己的名义，而不是以用户的名义，向“服务提供商”进行认证。

#### step1

客户端向认证服务器进行身份认证，并要求一个访问令牌。

```
  POST /token HTTP/1.1
     Host: server.example.com
     Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
     Content-Type: application/x-www-form-urlencoded

     grant_type=client_credentials
```

- grant*type：表示授权类型，此处的值固定为"client*credentials"，必选项。
- scope：表示权限范围，可选项。

#### step2

认证服务器确认无误后，向客户端提供访问令牌。

```
 HTTP/1.1 200 OK
     Content-Type: application/json;charset=UTF-8
     Cache-Control: no-store
     Pragma: no-cache

     {
       "access_token":"2YotnFZFEjr1zCsicMWpAA",
       "token_type":"example",
       "expires_in":3600,
       "example_parameter":"example_value"
     }
```