# 关于buu warmup的深入理解

打开页面，一个滑稽，查看源码，得到source.php，输入得到源码：

<?php
//首先测试一下in_array()函数的绕过方法
//$str=["heel","zzy"];
//var_dump(in_array("heel?f",$str));
    highlight_file(__FILE__);
    class emmm
    {
        public static function checkFile(&$page)
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];//检查白名单
            if (! isset($page) || !is_string($page)) {//第0重检查：是否设置了该变量或者说是是字符串
                echo "you can't see it";
                return false;
            }

            if (in_array($page, $whitelist)) {//第一重检查：是否在白名单，返回为真，注意该静态方法中有多重检查，只要满足一个return真即可
                return true;
            }
    
            $_page = mb_substr(//形同substr函数，加了前缀之后就可以识别中文了
                $page,
                0,
                mb_strpos($page . '?', '?')//strpos表示截取到第二个参数之前的位置，返回值为数字
            );
            if (in_array($_page, $whitelist)) {//第二重检查，经过裁剪的page是否在白名单
                return true;
            }
    
            $_page = urldecode($page);//url解码
            $_page = mb_substr(//梅开二度，再来一次
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {//又是一次熟悉的操作
                return true;//看着像是经过第一次检查，但是最重要的是经过了url解码
            }
            echo "you can't see it";
            return false;
        }
    }
    
    if (! empty($_REQUEST['file'])//要求file不能为空
        && is_string($_REQUEST['file'])//要求参数是字符串
        && emmm::checkFile($_REQUEST['file'])//要求得通过这个静态的检查
    ) {
        include $_REQUEST['file'];//如果通过了就包含文件
        exit;
    } else {
        echo "<br><img src=\"https://i.loli.net/2018/11/01/5bdb0d93dc794.jpg\" />";
    }  
?> 

是一道代码审计的题。将源码拷贝到本地进行分析。

这里主要运用了三个函数：

<?php

//in_array函数漏洞

$arr=[1,2,3];

var_dump(in_array('1shell.php',$arr,true));

echo"&lttbr&gt";

//mb_strpos函数说明

$str="hello world";

var_dump(mb_strpos($str,'w'));

echo"&ltbr&gt";

//mb_substr函数说明

var_dump(mb_substr($str,0,3));

?>

首先这里主要运用了这三个函数，我就去查了下这三个函数是否存在漏洞

$in_array(我们要搜索的，目标字符串); 判断我们要搜索的内容是否在数组里

返回值：布尔类型的值

$mb_strpos(原始的字符串，目标字符串)； 搜索第一次出现的位置

返回值：第一次出现的位置

$mb_substr(原始的字符串，起始位置，种植位置) 截取字符串

返回值：截取完毕后的字符串

对于in_array()函数，如果只有前两个参数

$arr=[1,2,3];

var_dump(in_array("1",$arr));

echo"&ltbr&gt";

对于这段代码，返回值是true，因为“1”==1 只比较值不比较类型，这里是弱类型比较

但如果加入第三个参数例如

var_dump(in_array("1",$arr,true));   ”1“！==1 这里就会返回false 因为会进行强类型比较，一个为字符串类型，一个为整数

这个函数的漏洞与PHP的弱类型比较一个道理比如说这里数组里有1-3，如果想传shell.php，就可以尝试1shell.php

对于第二个函数$mb_strpos(）是用来搜索字符串出现的位置，第三个$mb_substr(是用来截取字符串，这第三个函数的截取范围比如说是1-3，这里采取的是左闭右开区间的方式，了解了三个主要函数，来看源码。

if (! empty($_REQUEST['file'])//要求file不能为空
    && is_string($_REQUEST['file'])//要求参数是字符串
    && emmm::checkFile($_REQUEST['file'])//要求得通过这个静态的检查
) {
    include $_REQUEST['file'];//如果通过了就包含文件
    exit;

这一部分主要是以request的方法传递file，并且file不为空且这个file是字符串。如果这个还能通过上面定义的一个静态方法的检查，那么就可以包含这个文件。前面的几个要求都很容易满足，主要是通过静态方法的检查，我们来看这个类相关的代码。

    public static function checkFile(&$page)//file
        {
            $whitelist = ["source"=>"source.php","hint"=>"hint.php"];//检查白名单
            if (! isset($page) || !is_string($page)) {//第0重检查：是否设置了该变量或者说是字符串
                echo "you can't see it";
                return false;
            }
            if (in_array($page, $whitelist)) {//第一重检查：是否在白名单，返回为真，注意该静态方法中有多重检查，只要满足一个return真即可
                return true;
            }
    
            $_page = mb_substr(//形同substr函数，加了前缀之后就可以识别中文了
                $page,
                0,
                mb_strpos($page . '?', '?')//strpos表示截取到第二个参数之前的位置，返回值为数字
            );
            if (in_array($_page, $whitelist)) {//第二重检查，经过裁剪的page是否在白名单
                return true;
            }
    
            $_page = urldecode($page);//url解码
            $_page = mb_substr(//梅开二度，再来一次
                $_page,
                0,
                mb_strpos($_page . '?', '?')
            );
            if (in_array($_page, $whitelist)) {//又是一次熟悉的操作
                return true;//看着像是经过第一次检查，但是最重要的是经过了url解码
            }
            echo "you can't see it";
            return false;
        }
    }
首先定义了一个白名单，里面主要是俩source.php和hint.php，然后大致浏览下返回的值都是布尔值也就是true和false，也就是说上面的代码四个布尔值只要满足一个让他返回true就可以执行文件包含了。

第一个if主要是检查是否设置了该变量或者说是字符串，如果不是就返回不能看见，第二个if主要是检查是否在白名单，返回为真，就是file是否在白名单里没，然后就可包含在里面。然后就进行了截取函数，从0截取到 mb_strpos($page . '?', '?'),首先传入$file然后拼接一个？，然后再截取到?的位置，然后把这个位置返回到这个整数，然后截取了file，把截取完毕后的字符串复制给了$_page，再检查这个新变量是否在白名单，如果在，就返回true。

接着对$page进行url解码再赋值给$_page。然后就到了最重要的一部分

$_page = mb_substr(//梅开二度，再来一次
            $_page,
            0,
            mb_strpos($_page . '?', '?')
        );

这里看起来是梅开二度，其实变量已经被替换。上面的第一次是对$page进行一次操作，这里是对$_page进行操作，所以绕过的核心应该就是在这里进行。依旧是截取函数和数字函数，从0截取到问号，然后$__page解码后通过了白名单就返回true。这就要求？前面是白名单里内容，就是说构造内容应该是这样"source.php?dadda"通过验证就可以包含file变量了。

这里大致理清，回到hint.php.打开有一串文本“flag not here,and flag in ffffllllaaaagggg"这里可以大致构造了。因为我们要满足解码后是source.php?"dsss",然后截取后变成source.php就能通过。那解码之前就要url反解码，source.php%3f传到服务器进行解析就变成source.php?.但是因为这里要进行二次解析，所以要把%3f再进行一次url编码变成%253f.然后因为hint里提示ffffllllaaaagggg一共重复了四次，猜测要穿越四级目录。

所以构造payload:?file=source.php%253f/../../../../ffffllllaaaagggg。