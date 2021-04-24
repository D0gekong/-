







# bugku刷题记录（3）



### web12

打开环境，是一个登陆界面，尝试一下在用户名和密码都输入admin，回显告诉我们密码错误且不是本地IP需IP正确才可以，再回到题目描述本地管理员，可以猜想应该是需要改动ip，这里我用firefox的xforward插件把Ip改为127.0.0.1，再查看f12，发现一串base64，解码为test12，尝试输入为密码，得到flag。

### web13

打开环境是一个提交框，上面提示看看源代码，F12里发现三段URL编码

%66%75%6e%63%74%69%6f%6e%20%63%68%65%63%6b%53%75%62%6d%69%74%28%29%7b%76%61%72%20%61%3d%64%6f%63%75%6d%65%6e%74%2e%67%65%74%45%6c%65%6d%65%6e%74%42%79%49%64%28%22%70%61%73%73%77%6f%72%64%22%29%3b%69%66%28%22%75%6e%64%65%66%69%6e%65%64%22%21%3d%74%79%70%65%6f%66%20%61%29%7b%69%66%28%22%36%37%64%37%30%39%62%32%62

%61%61%36%34%38%63%66%36%65%38%37%61%37%31%31%34%66%31%22%3d%3d%61%2e%76%61%6c%75%65%29%72%65%74%75%72%6e%21%30%3b%61%6c%65%72%74%28%22%45%72%72%6f%72%22%29%3b%61%2e%66%6f%63%75%73%28%29%3b%72%65%74%75%72%6e%21%31%7d%7d%64%6f%63%75%6d%65%6e%74%2e%67%65%74%45%6c%65%6d%65%6e%74%42%79%49%64%28%22%6c%65%76%65%6c%51%75%65%73%74%22%29%2e%6f%6e%73%75%62%6d%69%74%3d%63%68%65%63%6b%53%75%62%6d%69%74%3b





将三段url编码分别解码得到

```
function checkSubmit(){var a=document.getElementById("password");if("undefined"!=typeof a){if("67d709b2b
```

```
aa648cf6e87a7114f1"==a.value)return!0;alert("Error");a.focus();return!1}}document.getElementById("levelQuest").onsubmit=checkSubmit;
```

54aa2

将这三段代码进行拼接，中间得到这样一67d709b2b54aa2aa648cf6e87a7114f154aa2并结合eval函数的作用，直接提交，就能获得flag。

![image-20210419162121584](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419162121584.png)

### web14

打开环境，只有一个链接，点击

![image-20210419165434792](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419165434792.png)

点开链接后显示一个文本index.php,f12也没啥信息。注意到url有个file参数，猜一波文件包含，但我是不会文件包含的。这里参考了大佬的WP。

使用php协议进行读取：file=php://filter/read=convert.base64-encode/resource=index.php

先对这个协议进行一波解释：

> file进行get传参，题目前面提示我们的index.php，php://filter/是访问本地文件的协议，read表示要读取的链的筛选列表，resource表示访问的目标文件。
>
> 为什么中间要进行一波base64加密呢？因为不进行base64加密，会直接当成php文件执行。而我们传递的参数被include()函数引入了base64的格式，执行不成功，所以会返回文件的源码。
>
> 使用php文件协议后，页面返回一串被base64加密的源码
>
> 解密base64后查找即可获得flag

![image-20210419165333519](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419165333519.png)

### web15

进来是一个输入密码的登陆

![image-20210419215257586](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419215257586.png)

老规矩。按照题目提示，使用burpsuite抓包爆破密码，因为是五位数字，就从10000-99999

![image-20210419215337651](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419215337651.png)

爆破成功查看响应头，获得flag。

![image-20210419215412637](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210419215412637.png)

### web16

打开页面是一串字符串，没看懂啥意思

![image-20210420112449418](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210420112449418.png)



按照题目提示“备份是个好习惯”应该是有备份文件泄露，这里用御剑扫一下

![image-20210420113005774](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210420113005774.png)

发现bak文件下载下来打开查看

![image-20210420113107113](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210420113107113.png)

源码意思是，对key进行一个过滤，然后传参key1和key2两个参数，同时key1和key2的md5值要相等，并且未经过md5加密前的值不能相等，满足条件就会输出flag。

这里绕过过滤key以及绕过md5参考了大佬wp

**==漏洞：**

> 如果两个字符经MD5加密后的值为 0exxxxx形式，就会被认为是科学计数法，且表示的是0*10的xxxx次方，还是零，都是相等的。
>
> 下列的字符串的MD5值都是0e开头的：
>
> QNKCDZO
>
> 240610708
>
> s878926199a
>
> s155964671a
>
> s214587387a
>
> s214587387a

**数组漏洞：**

md5()函数无法处理数组，如果我们传数组进去，会返回一个NULL，所以两个数组经过加密后得到的都是相同的NULL。

![image-20210420114327524](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210420114327524.png)

### web17

（是一道sql注入题目，第一次接触sql注入题目，全程按照wp）

这里我采取手工注入，也可使用sqlmap进行注入。

根据页面内容，还有输入框的提示1,2,3，猜测应该是sql注入的类型，尝试传参一个1

![image-20210421164340378](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164340378.png)

有回显，再加个’号测试测试

![image-20210421164357305](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164357305.png)

发现没有回显，加一个注释试试

![image-20210421164416555](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164416555.png)

有数据回显，判定是存在sql注入，我们查查应该有多少列，其实看网页显示的数据，就能看出是4列了，但还是测试一下，免得判断错误

![image-20210421164432714](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164432714.png)

![image-20210421164446156](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164446156.png)

发现为4时有回显数据，为5时没有数据回显，判断有4列

那我们直接来尝试联合注入，查查数据库名字，这里记得要写成id=-1’，把前面查询的数据置空，当然id=0也是可以的

![image-20210421164502086](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164502086.png)

查出数据库的名字为：skctf_flag

继续查表名字

![image-20210421164519524](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164519524.png)

查出有两个表，分别为fl4g和sc，我们先查表为fl4g的列名![image-20210421164552837](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164552837.png)



得到列名为skctf_flag，下一步查看列里面的数据

![image-20210421164611615](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421164611615.png)

得到flag。

后记：第一次做sql注入类型题，感觉有稍微复杂，需要去补一下基础知识，这题权当了解一个基本流程以及开端。

### web18

打开页面，是一个很长的计算式子，而且2s内又会发生变化,翻了一下网页源码也没有什么有用的信息，猜测是需要我们向他发送一个post数据包，数据为式子的计算值，但它没有给出需要传参的对象是什么。而且在2s内计算出值不太可能，这个时候我们需要写一个python脚本跑一跑.

这里采用网上找到的脚本（老脚本小子了）

```
import requests
import re
url = ''
session = requests.session()
a = re.findall('<div>([0-9+*-]*)?', session.get(url).text)
#因为re.findall返回的是一个列表，所以我们查看一下列表的内容
print("页面的计算式子：",a[0],'\n')
b = eval(a[0])
c = {"":b}
result = session.post(url,c)
print(result.text)
```

先解释一下脚本，用session保持一个会话状态，免得下次post出去的时候就是另一个计算值了，然后用正则匹配页面内容div后面的数字和±*三种运算符，后面的表示匹配全部，?表示非贪婪匹配，用eval计算我们的式子。

首先我们给一个空的对象传计算的值测试测试，因为我们也不知道给谁传值

![image-20210421165007691](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421165007691.png)

返回页面结果，提示我们给value传值，ok，那我们在字典上再填上value，跑脚本就可得到flag。

### web19

打开环境，只有一个文本“我感觉你得快点”，看看f12里有啥消息。

![image-20210421170140849](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421170140849.png)

这里说我们应该用post把将margin的值传过去，用hackbar传去看响应头，发现flag。

![image-20210421170226139](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210421170226139.png)

但经过两次base64解码后发现并不正确，回到环境回显说“我都说了让你快点”，应该是这个flag会刷新。

这里采用脚本进行运算

```
import requests
import base64
url = ''
se = requests.session()
flag = se.get(url).headers['flag']
flag = base64.b64decode(flag).decode()#多加个decode(),是因为上一步生成的是bytes类型，要转换成string
flag = base64.b64decode(flag.split(":")[1]) 
go = {'margin':flag}
print(se.post(url,data=go).text)
```

还是解释一波脚本吧，首先使用session保持一个会话状态，然后得到返回的响应头中的flag那一条数据。经过第一次base64解密，然后取解密出来的那一串字符串中的‘：’后面的那一串base64加密的数据进行解密，最后发送一个post数据包，打印出文本信息。

### web20

打开环境，发现一大串没见过的编码，经过多次解密也没发现是什么编码（若能指出，纯属我菜）

![image-20210423093444305](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210423093444305.png)

放弃解密编码，在Url上的filename上发现后面的值为base64，拿去解密，得到keys.txt.

以这个为突破点，访问下index.php,因为keys.txt为base64编码，所以这里也要把index.php进行Base64化,Base64后的编码为aW5kZXgucGhw。

传过去后f12发现一些有趣的东西。

![image-20210423093825035](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210423093825035.png)

他并没有显示全部的源代码，这里把思路转换到另一个参数line上。对line进行传值，首先传1试试，得到回显。

![image-20210423093928595](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210423093928595.png)

接着继续传参2试试，仍然得到回显。

![image-20210423094143232](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210423094143232.png)

每次传过去的数字不同，回显得到的内容也不相同，这里可以采取用脚本得到完整的源码，但这里我并不会编写脚本（老菜狗了），经过我尝试，从1传到18就可以得到完整的源码

```
<?php

error_reporting(0);

	$file=base64_decode(isset($_GET['filename'])?$_GET['filename']:"");

	$line=isset($_GET['line'])?intval($_GET['line']):0;

	if($file=='') header("location:index.php?line=&filename=a2V5cy50eHQ=");

	$file_list = array(

	'0' =>'keys.txt',

	'1' =>'index.php',

);

 

if(isset($_COOKIE['margin']) && $_COOKIE['margin']=='margin'){

	$file_list[2]='keys.php';

}



if(in_array($file, $file_list)){

	$fa = file($file);

	echo $fa[$line];

}

?>
```

审计源码后得到知道需要传参cookies，且要margin=margin才能访问keys.php文件，keys.php需要进行Base64编码，我们用hackbar进行传参

![image-20210423095439584](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210423095439584.png)

在f12里得到flag.

## 暂时告一段落，未完断续。