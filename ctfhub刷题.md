# ctfhub刷题

在各大平台刷题感觉自己的基础并不扎实，来ctfhub学习下知识。

1.请求方式
1.1 先了解一下http协议
HTTP，即超文本传输协议，是一种实现客户端和服务器之间通信的响应协议，它是用作客户端和服务器之间的请求。HTTP默认使用80端口，这个端口指的是服务端的端口，而客户端使用的端口是动态分配的。当我们没有指定端口访问时，浏览器会默认帮我们添加80端口。

客户端（浏览器）会向服务器提交HTTP请求；然后服务器向客户端返回响应；其中响应包含有关请求的状态信息，还可能包含请求的内容。


### 1.2 http请求方法

![img](https://img-blog.csdnimg.cn/20201116222733190.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lzaHNoc2o=,size_16,color_FFFFFF,t_70#pic_center)

### 1.3 客户端请求消息

客户端发送一个HTTP请求到服务器的请求消息包括以下格式：请求行（request line）、请求头部（header）、空行和请求数据四个部分组成，下图给出了请求报文的一般格式。

![img](https://img-blog.csdnimg.cn/20201117185948837.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lzaHNoc2o=,size_16,color_FFFFFF,t_70#pic_center)

1.4 curl命令
curl 是常用的命令行工具，用来请求 Web 服务器。它的名字就是客户端（client）的 URL 工具的意思。

不带有任何参数时，curl 就是发出 GET 请求：

curl https://www.baidu.com
1
抓取页面内容到一个文件里：
-O：把输出写到该文件中，保留远程文件的文件名
（ps： -O 是大写的喔）

curl -O home.html  http://www.baidu.com
1
查看交互过程：

curl -v http://www.baidu.com
1
请求方法：
-X 指定http请求方式。

curl -X http请求方式 http://www.baidu.com 
1

用GET方式访问：curl -X GET http://www.baidu.com 
用POST方式访问：curl -X POST http://www.baidu.com 1.5 wp


打开后，从给的hint来看是要用**“CTFHUB”**的请求方式来访问就可以得到flag了。并且要注意请求方式的大小写喔!


还没在windows安装curl。。。只能打开kali来使用curl了。

命令：

curl -X CTFHUB -v http://challenge-eb70d1e7bd395dec.sandbox.ctfhub.com:10080/index.php
1

于是开开心心的拿着财富密码去提交啦~~

ctfhub{f1da689c97ec98a3f62c32a0}
1
2. 302跳转
2.1 HTTP状态码分类
1**	信息，服务器收到请求，需要请求者继续执行操作
2**	成功，操作被成功接收并处理
3**	重定向，需要进一步的操作以完成请求
4**	客户端错误，请求包含语法错误或无法完成请求
5**	服务器错误，服务器在处理请求的过程中发生了错误
302会进行临时的重定向跳转

302	Found	临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI
2.2 wp


点击图中的“Give me Flag”没有跳转，查看源代码好像也没什么改变。于是用burpsuit抓包来看一下，数据包的请求和回应。

抓到包后 右键 sent to Repeater。


点击send后，有ctfhub{3e3abf89c66bb88da8a6e2cd}
1
3.Cookie
3.1 什么是Cookie
Cookie实际上是一小段的文本信息（key-value格式）。客户端向服务器发起请求，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。

比如，我们刚买手机的时候会配置锁屏密码、指纹、脸部识别等等的这些个人特征信息。当你想要打开手机的时候，手机能识别你的这些特征，从而解锁屏幕。

3.2 wp

进入后，根据只有admin才能得到flag，结合标题得知应该是修改cookie进行cookie欺骗

用burpsuit抓到包后，发现cookie的admin=0，把包sent to Repeater后， 把admin=0改为admin=1，再把包发出去就可以得到flag。

ctfhub{54679b9ffa14fde7bee0e22c}
1

4. 基础认证
4.1 基本认证介绍
认证概念：
服务器需要通过某种方式来了解用户的身份，一旦服务器知道了用户的身份，就可以判定用户可以访问事务和资源了；通常通过用户名和密码；

HTTP的两个官方的认证协议：基本认证和摘要认证
认证的四个步骤：
请求： 客户端发起一条请求；第一条请求没有认证消息；
质询： 服务器对客户端进行质询；返回一条401 Unauthorized响应，并在www-Authenticate首部说明如何以及在哪里进行认证；一般指定对哪个安全域进行认证；
授权：客户端收到401质询，弹出对话框，询问用户名和密码，用户输入用户名和密码后，客户端会用一个冒号将其连接起来，编码成“经过扰码的”Base-64表示形式，然后将其放在Authorization首部中回送；
成功： 服务器对用户名和密码进行解码，验证它们的正确性，然后用一条HTTP 200 OK报文返回所请求的报文；

4.2 wp

下载好附件打开后，发现是一个密码本，可以联想到，一会需要爆破。

打开网站后，发现需要爆破admin。

用burpsuit抓包，看到了base64加密过的基础认证码（因为有=号，所以可以确定是base64，当然 也可由前面的介绍得知。）


sent to Intruder，准备爆破啦啦啦啦。



解密完后得到账号密码（两次做题的密码是不一样的。。做的时候还是要以真实环境的为准。）

又得到flag啦，可开心了。


ctfhub{dee04f75f2edd8f168eaec1f} 
1
5.响应包源代码


按照题目直接查看网页源代码就可以获得flag了喔。

果然hint还是不会骗人哒，出题人是老实人哈哈。

ctfhub{d090d1c65d23de658356d3f3}
