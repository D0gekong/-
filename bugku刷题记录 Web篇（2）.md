# bugku刷题记录 Web篇（2）

### web5

打开环境，是一段Php代码，进行代码审计。



![image-20210416160326692](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416160326692.png)

代码大概意思就是用Get方式进行传输num参数，如果传输的参数不是数字，就回显这个参数。如果这个参数等于1，就回显flag。进行尝试。

我个人理解是进行绕过，让这个参数其值等于1但是表达形式不是直接为1，比如说一些计算例如1*1,1^1,这些计算结果都等于1.

（标准理解：**is_numeric()** 函数用于检测变量是否为数字或数字字符串。

题意是get传参num不能为数字或数字字符串但值却等于1。

当num用科学计数法表示1：1*e*0

或者传入1x，x为任意字母串即可。）得flag。

![image-20210416161554391](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416161554391.png)

### web6

打开环境，弹出弹窗提示flag就在这里，点击确认叫我们在这里找一找。但一直点，一直是出现这两个弹窗。点击右键也没任何反应。这里采取使用F12或者是快捷键ctrl+shift+i，经过查找发现这样一段编码

![image-20210416162152902](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416162152902.png)

这是一段Unicode编码，扔进在线工具进行解码即可获得flag

![image-20210416162300367](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416162300367.png)

### web7

打开环境，网页里一直在进行图片的消失与重现，经过题目提示，我们应该让图片消失这种现象停止下来，遇事不决f12查看，打开source，发现每当图片出现时，就会出现flag，若是截图手速快可以直接暴力解题。但是进行代码审计，发现代码里存在myrefresh这个函数，经过查询资料这个函数是指定一定时间内进行刷新一次。

![image-20210416163012584](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416163012584.png)

这里设定了每0.5秒进行刷新一次，我这里进行修改代码，修改到适合的时间，方便我进行获取flag。或者说关闭Js也可，即可获取flag。

![image-20210416163323641](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210416163323641.png)



### web8

打开环境，是一段PHP代码，进行代码审计。

![image-20210418142307729](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418142307729.png)

inclue"flag.php"说明本地包含了flag.php这个文件，request[]这个函数经查找资料可经由get和post两种方式传递。然后eval()这个函数是将字符串当做代码进行运算。var_dump（）这个函数是括号里的参数先判断类型再全部打印出来。根据eval()函数的特性尝试直接将flag.php输出

构造payload:/?hello=1);print_r(file("./flag.php"));%23

由于这里需要打开flag.php这个文件，我们采用print_r.

这里给出一点小知识：print（）  只能打印出简单类型变量的值(如int,string)  
print_r（） 可以打印出复杂类型变量的值(如数组,对象) 
echo   输出一个或者多个字符串

);表示强制结束的作用，如果要打开文件file()里记得引号，./这里是linux目录的知识。最后的%23是url编码，是#为注释的意思，因为前面强制执行了结束，所以最后要补一个注释（个人理解）。

理清步骤，运行payload得到flag。

![image-20210418145106353](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418145106353.png)

这里看其他大佬的wp还有另外一种解法，比较简单。直接对Hello赋值hello=file_get_contents('flag.php')，然后按f12就可以获得flag。highlight_file("flag.php")，把highlight_file换成file_get_contents  readfile  show_resource file 都行都能

还有一种最简hello=`nl *`反引号里的会被当做代码执行,nl是带行号显示文件,*是通配符

### web9

打开环境，一段php代码，提示说flag在变量里。代码审计后发现只存在args这一个变量，也并没有说如何通过变量获得flag，考虑flag进行了隐藏，这里采取全局变量GLOBALS进行尝试

![image-20210418151916306](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418151916306.png)

global的作用就是引用全局作用域中可用的全部变量，global变量在Php的任何地方都可以用。

构造payload：/?args=GLOBALS，获得flag。![image-20210418152053119](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418152053119.png)

### web10

打开环境，只有一段“什么也没有的文本”，打开f12,查看源码也没有，最后在networ里的响应头找到flag

![image-20210418154148039](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418154148039.png)

### web11

打开环境，就是一个简单的网页，根据提示说是留有后门，尝试用御剑扫描一下

![img](https://img2020.cnblogs.com/blog/1961333/202010/1961333-20201016111346458-1920392867.png)

发现shell.php这个，进去后是一个需要输入密码的网页，尝试用burp进行爆破，随后就能获得flag。

![image-20210418165702680](C:\Users\16068\AppData\Roaming\Typora\typora-user-images\image-20210418165702680.png)

这里贴上比较详细操作过程网址https://www.cnblogs.com/jingvf/p/13825442.html。





