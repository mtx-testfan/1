---
typora-copy-images-to: img
---

# http协议讲解

## HTTP协议简介

## 0.0. HTTP协议简介

超文本传输协议（HyperText Transfer Protocol） 是一种应用层协议。 HTTP是万维网的数据通信的基础。设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法 ![img](D:\company\零基础视频录制\img\http简介.png) ![img](D:\company\零基础视频录制\img\蒂姆.png)

## 1. 使用谷歌/火狐浏览器分析

在Web应用中，服务器把网页传给浏览器，实际上就是把网页的HTML代码发送给浏览器，让浏览器显示出来。而浏览器和服务器之间的传输协议是HTTP，所以：

- HTML是一种用来定义网页的文本，会HTML，就可以编写网页；
- HTTP是在网络上传输HTML的协议，用于浏览器和服务器的通信。

Chrome浏览器提供了一套完整地调试工具，非常适合测试人员做接口测试去用。

安装好Chrome浏览器后，打开Chrome，在菜单中选择“视图”，“开发者”，“开发者工具”，就可以显示开发者工具：

![img](D:\company\零基础视频录制\img\Snip20160908_1.png)

#### 说明

- Elements显示网页的结构
- Network显示浏览器和服务器的通信

我们点Network，确保第一个小红灯亮着，Chrome就会记录所有浏览器和服务器之间的通信：

![img](D:\company\零基础视频录制\img\Snip20160908_2.png)

## 2. http协议的分析

当我们在地址栏输入www.sina.com时，浏览器将显示新浪的首页。在这个过程中，浏览器都干了哪些事情呢？通过Network的记录，我们就可以知道。在Network中，找到www.sina.com那条记录，点击，右侧将显示Request Headers，点击右侧的view source，我们就可以看到浏览器发给新浪服务器的请求：

### 2.1 浏览器请求

![img](http://49.233.200.77:8000/miniweb/Images/13day/Snip20160908_3.png)

![img](D:\company\零基础视频录制\img\Snip20160908_5.png)

#### 说明

最主要的头两行分析如下，

![img](D:\company\零基础视频录制\img\2964446-fdfb1a8fce8de946.png)

第一行：

```
    GET / HTTP/1.1
```

GET表示一个读取请求，将从服务器获得网页数据，/表示URL的路径，URL总是以/开头，/就表示首页，最后的HTTP/1.1指示采用的HTTP协议版本是1.1。目前HTTP协议的版本就是1.1，但是大部分服务器也支持1.0版本，主要区别在于1.1版本允许多个HTTP请求复用一个TCP连接，以加快传输速度。

从第二行开始，每一行都类似于Xxx: abcdefg：

常见的请求头如下：

```
Host :主机和端口号
Connection ：连接类型
Upgrade-lnsecure-Requests：升级为https请求
User-Agent:浏览器名称
Accept：传输文件类型
Referer：页面跳转处
Accept-Encoding：文件编解码格式
Cookie：Cookie
x-requested-with :XMLHttpRequest(是Ajax异步请求)
```



### 2.2 服务器响应

继续往下找到Response Headers，点击view source，显示服务器返回的原始响应数据：

![img](D:\company\零基础视频录制\img\Snip20160908_6.png)

HTTP响应分为Header和Body两部分（Body是可选项），我们在Network中看到的Header最重要的几行如下：

![img](D:\company\零基础视频录制\img\2964446-1c4cab46f270d8ee.jpg)

```
    HTTP/1.1 200 OK
```

200表示一个成功的响应，后面的OK是说明。

如果返回的不是200，那么往往有其他的功能，例如

- 失败的响应有404 Not Found：网页不存在
- 500 Internal Server Error：服务器内部出错
- ...等等...

```
    Content-Type: text/html
```

Content-Type指示响应的内容，这里是text/html表示HTML网页。

> 请注意，浏览器就是依靠Content-Type来判断响应的内容是网页还是图片，是视频还是音乐。浏览器并不靠URL来判断响应的内容，所以，即使URL是`http://www.baidu.com/meimei.jpg`，它也不一定就是图片。

HTTP响应的Body就是HTML源码，我们在菜单栏选择“视图”，“开发者”，“查看网页源码”就可以在浏览器中直接查看HTML源码：

![img](D:\company\零基础视频录制\img\Snip20160908_7.png)

#### 浏览器解析过程

> **当浏览器读取到新浪首页的HTML源码后，它会解析HTML，显示页面，然后，根据HTML里面的各种链接，再发送HTTP请求给新浪服务器，拿到相应的图片、视频、Flash、JavaScript脚本、CSS等各种资源，最终显示出一个完整的页面。所以我们在Network下面能看到很多额外的HTTP请求。**

![img](D:\company\零基础视频录制\img\Snip20160908_8.png)

## 3. 总结

### 3.1 HTTP请求

跟踪了新浪的首页，我们来总结一下HTTP请求的流程：

#### 3.1.1 步骤1：浏览器首先向服务器发送HTTP请求，请求包括：

> 方法：GET还是POST，GET仅请求资源，POST会附带用户数据；
>
> 路径：/full/url/path；
>
> 域名：由Host头指定：Host: www.sina.com
>
> 以及其他相关的Header；
>
> 如果是POST，那么请求还包括一个Body，包含用户数据

#### 3.1.1 步骤2：服务器向浏览器返回HTTP响应，响应包括：

> 响应代码：200表示成功，3xx表示重定向，4xx表示客户端发送的请求有错误，5xx表示服务器端处理时发生了错误；
>
> 响应类型：由Content-Type指定；
>
> 以及其他相关的Header；
>
> 通常服务器的HTTP响应会携带内容，也就是有一个Body，包含响应的内容，网页的HTML源码就在Body中。

#### 3.1.1 步骤3：如果浏览器还需要继续向服务器请求其他资源，比如图片，就再次发出HTTP请求，重复步骤1、2。

> Web采用的HTTP协议采用了非常简单的请求-响应模式，从而大大简化了开发。当我们编写一个页面时，我们只需要在HTTP请求中把HTML发送出去，不需要考虑如何附带图片、视频等，浏览器如果需要请求图片和视频，它会发送另一个HTTP请求，因此，一个HTTP请求只处理一个资源(此时就可以理解为TCP协议中的短连接，每个链接只获取一个资源，如需要多个就需要建立多个链接)

HTTP协议同时具备极强的扩展性，虽然浏览器请求的是`http://www.sina.com`的首页，但是新浪在HTML中可以链入其他服务器的资源，比如`<img src="http://i1.sinaimg.cn/home/2013/1008/U8455P30DT20131008135420.png">`，从而将请求压力分散到各个服务器上，并且，一个站点可以链接到其他站点，无数个站点互相链接起来，就形成了World Wide Web(万维网)，简称WWW。

![img](D:\company\零基础视频录制\img\Snip20160908_9.png)

### 3.2 HTTP格式

每个HTTP请求和响应都遵循相同的格式，一个HTTP包含Header和Body两部分，其中Body是可选的。

HTTP协议是一种文本协议，所以，它的格式也非常简单。

#### 3.2.1 HTTP GET请求的格式：

```
    GET /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3
```

每个Header一行一个，换行符是\r\n。

#### 3.2.2 HTTP POST请求的格式：

```
    POST /path HTTP/1.1
    Header1: Value1
    Header2: Value2
    Header3: Value3

    body data goes here...
```

当遇到连续两个\r\n时，Header部分结束，后面的数据全部是Body。

#### 3.2.3 HTTP响应的格式：

````
    200 OK
    Header1: Value1
    Header2: Value2
    Header3: Value3

    body data goes here...
```

HTTP响应如果包含body，也是通过\r\n\r\n来分隔的。

请再次注意，Body的数据类型由Content-Type头来确定，如果是网页，Body就是文本，如果是图片，Body就是图片的二进制数据。

当存在Content-Encoding时，Body数据是被压缩的，最常见的压缩方式是gzip，所以，看到Content-Encoding: gzip时，需要将Body数据先解压缩，才能得到真正的数据。压缩的目的在于减少Body的大小，加快网络传输。

### 3.3 http协议的特点

http协议，超文本传输协议，基于tcp/ip协议进行传输，现在使用的是HTTP 1.1
主要特点：
1、简单快速：客户向服务器请求服务时，只需传送请求方法(method)和路径(url)。
请求方法常用的有GET,POST,PUT,DELETE。每种方法规定了客户与服务器联系的类型不同。
由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
2、灵活：HTTP允许传输任意类型的数据对象。传输类型由Content-Type加以标记。
3、支持B/S及C/S模式
4、无连接：无连接的含义是限制每次连接只处理一个请求。
服务器处理完客户的请求，并收到客户的应答后，即断开连接。
采用这种方式可以节省传输时间。(HTTP1.1开始支持长连接，需要服务端配置相应的参数)
5、无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。
另一方面，在服务器不需要先前信息时它的应答就较快。

![img](D:\company\零基础视频录制\img\877318-20180418160546133-1479186889.png)

### 3.4 url地址解析

url是全称是UniformResourceLocator, 中文叫统一资源定位符,是互联网上用来标识某一处资源的地址

```
protocol :// hostname[:port] / path / [;parameters][?query]
```

http://www.mtongxue.com:80/home/index.html?id=251&page=1

1.传送协议。
2.层级URL标记符号(为[//],固定不变)
3.服务器。（通常为域名，有时为IP地址）
4.端口号。（以数字方式表示，若为HTTP的默认值“:80”可省略）
5.路径。（以“/”字符区别路径中的每一个目录名称）
6.查询。（GET模式的窗体参数，以“?”字符为起点，8.每个参数以“&”隔开，再以“=”分开参数名称与数据，通常以UTF8的URL编码，避开字符冲突的问题

### 3.5 常用的请求方式

根据HTTP标准，HTTP请求可以使用多种请求方法。
HTTP1.0定义了三种请求方法： GET, POST 和 HEAD方法。
HTTP1.1新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT 方法。

```
GET     请求指定的页面信息，并返回实体主体。
HEAD     类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头
POST     向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改。
PUT     从客户端向服务器传送的数据取代指定的文档的内容。
DELETE      请求服务器删除指定的页面。
CONNECT     HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器。
OPTIONS     允许客户端查看服务器的性能。
TRACE     回显服务器收到的请求，主要用于测试或诊断。
```



### 3.6 响应状态码总结

![img](D:\company\零基础视频录制\img\877318-20180418161321986-304902913.png)



