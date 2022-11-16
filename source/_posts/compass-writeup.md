---
title: compass-writeup
date: 2022-07-09 17:29:52
tags: ctf
---

我也没想到我第一篇博客会是ctf...其实之前有很多可以写的东西，但那基本上是高中的时候，我没有稳定的环境（其实就是自己不会）跑hexo（确信）。

总之，目前有在参加[COMPASS](https://wiki.compass.college/)的培训。打不打比赛不知道，主要还是想图一乐加上了解一点点security。然后我需要点地方记笔记，就把这个远古page拿过来用了。

以下是正文，会持续更新。

<!-- more -->

## Week 1
### [旅行照片](https://compass.ctfd.io/challenges#Week1/%E6%97%85%E8%A1%8C%E7%85%A7%E7%89%87-216)
蓝色kfc....没了。当时绕着海岸线找了一圈面向南+桥+港口的组合，最后发现百度地图上好像少了一个岛。真的屑。


## Week 2
### [Calculat3 M3](https://compass.ctfd.io/challenges#Week2/Calculat3%20M3-229), a.k.a https://web.ctflearn.com/web7/
很简单的一个题，除了我以为里面用的是`eval`...  
实际上大概是`shell_exec(不知道什么只能解析带空格的算式的命令);`。随便post一个`1; ls`就结束了。

### [php audit](https://compass.ctfd.io/challenges#Week2/php%20audit-232)
flag在php那个`GLOBALS`全局变量里。想了好久才想起来有这个玩意。

### [Execute?](https://compass.ctfd.io/challenges#Week2/Execute?-231)
首先，经过提醒才想起来F12，看到可以view-source看php源码。这题用了`str_replace()`换掉了一堆符号，让我发现自己是个linux废物。最后实在想不出（查了一堆怎么绕过trim+str_replace），直接去查了原题。  
答案是，`shell_exec`会执行传进去的**每一行**指令。所以可以用`\n`，也就是`0x0A`分隔。  
最后ls, cat结束。一个小插曲是cat出的东西是html注释，耽误了一小会。

### [why sqli](https://compass.ctfd.io/challenges#Week2/why%20sqli?-233)
用了`stripslashes`和`htmlentities`过滤。前者去除GET时用`\`的转义，后者把引号变成&amp。  
上周培训完要走的时候听某位神仙高呼“这都能宽字节？”，以为听到了解法，去学了一手。  
总体就是在`\`前面加个`%DF`把`\`吃掉...但是没成功。现在看来引号都没了把`\`干掉也没用...上当！  
最后还是去查了原题。代码里构造查询用的是
``` php
$query='SELECT * FROM users WHERE name=\''.$username.'\' AND pass=\''.$password.'\';';
```
拆一下：
``` sql
SELECT * FROM users WHERE name='$username' AND pass='$password';
```

查到的题解是在username最后插个`\`，让查询变成
``` php
# $username = "user\"
$query="SELECT * FROM users WHERE name=\'user\\' AND pass=\'".$password.'\';';
```
这样可以把`AND`前面的`'`转义掉，password就可以不考虑`pass='`的闭合问题了...  
最后构造的是`http://103.102.44.218:10018/challenge1.php?username=user\&password= or 1=1 order by pass limit 1 offset 1--+`。

### [For you, PHP Inclusion master](https://compass.ctfd.io/challenges#Week2/For%20you,%20PHP%20Inclusion%20master-234)
*我又不是master，我怎么会做*  
0 solve，不知道是神仙都做不出来还是不样神仙做题。反正我肯定不会，等听了题解再更新。


## Week 3
### [SQLI](https://compass.ctfd.io/challenges#Week3/SQLI-238)
一眼mysql_error，去查了一下还真能注入...  
然后网上随便找了个`' and exp(~(select * from (select pw from php)x)) #`。注入成功把flag打出来了，交上去提示不对。  
问了问神仙，拿出来的可能是别的题的flag...草。真正的flag需要到echo "*******"那里。  
echo前面用的是`!strcasecmp($pass, $row[pw])`判断密码。网上说给strcasecmp传数组会返回0，但\$pass过了遍md5，没法用数组。  
最后考虑到...反正随便注入，给`$row[pw]`换成自己想要的值就好了。
构造的是user=`' union select "c4ca4238a0b923820dcc509a6f75849b" as pw #`, pass=`1`。c4ca那一串是1的md5。

### [Reverse shell included](https://compass.ctfd.io/challenges#Week3/Reverse%20shell%20included-235)
~~虽然名字叫Reverse shell但其实用不上反弹shell~~
``` php
$str=@(string)$_GET['str'];
eval('$str="'.addslashes($str).'";');
```
这里用到了`${}`语法。php会先执行`${}`里写的函数，然后把返回值拼进字符串。  
e.g. `?str=${phpinfo()}`会打出phpinfo。  
不过有个问题，有addslashes，所以字符串需要...通过GET传进来。  
最后构造的是`?str=${eval($_GET[1])}&1=echo%20file_get_contents($_GET[2]);&2=flag.php`。

反弹shell的话，vps上用`nc -lvnp port`，target照着[your-shell](https://your-shell.com)上的指示来就行。

# Week 4
### [WhitePages](https://compass.ctfd.io/challenges#Week4/WhitePages-246)
hex打开一看，一共就四种字符。一开始用的是0x20为分割，其他的计数转八进制，然后赛博厨子没结果。后来把0x20为0，另外一个为1，还是没结果。最后有群友截图问，发现0x20是1，另一个是0...麻了。最后往from binary一丢就完事了。

### [简单的隐写](https://compass.ctfd.io/challenges#Week4/%E7%AE%80%E5%8D%95%E7%9A%84%E9%9A%90%E5%86%99-251)
这题抄的wp。扔给stegSolve，在RGB通道看到最上面一行有不规则的点...然后没有然后了。

### [m00nwalk](https://compass.ctfd.io/challenges#Week4/m00nwalk-245)
SSTV。

### [weak password](https://compass.ctfd.io/challenges#Week4/weak%20password-248)
先用john把压缩包密码破了。  
密码是5位可打印的ASCII，翻了下john的文档，可以直接配置。
``` ini
# /etc/john/john.conf
[Incremental:Custom]
File = $JOHN/ascii.chr
MinLen = 5
MaxLen = 5
CharCount = 95
```
然后用
``` shell
zip2john xxxtentacion.zip >> passwd
john --incremental:Custom passwd
```
就可以爆了。  
解出一张里面是南通内容的图片，格式jpg。jpg不能LSB（一开始忘记了，看了好久），扔binwalk和stegdetect也没结果。最后拿hex开了一下，发现jpg最后多了点东西。复制出来扔赛博厨子的magic，给出了base64+png。  
这张图片是个反色缺了三个角的二维码。补上三个角可以拿到flag的后半段，png本体丢进`zsteg -a`可以拿到flag前半段。

### [老坛酸菜之神](https://compass.ctfd.io/challenges#Week4/%E8%80%81%E5%9D%9B%E9%85%B8%E8%8F%9C%E4%B9%8B%E7%A5%9E-250)
据说很简单...总之有好多解法。
#### CE
要找100个老坛酸菜的图片，所以找到一个就用CE搜increased by 1...  
最后搜到的地址是有规律的，都改成99就行。  
*草啊我手机修改了那么多次游戏怎么在这就没想到*

#### OD（其一）
据说可以给ESP寄存器下断点，有个叫ESP定理的东西。  
ESP好像很像mips的`$ra`...  
总之没成功。

#### OD（其二）
根据[这篇博客](https://immoying.top/?p=192)，先给MessageBox下断点然后单步调试，易语言的主窗口是`call+add+jmp`，在这里搜索字符串。

#### 脱壳
百度一个易语言脱壳工具，然后直接文本文件打开看字符串即可。

# Week 5
本周的题目来自[这里](https://hope.dicega.ng/challs)  
### crypto/obp
儿简送的xor。从之前的一个misc知道了flag格式是`hope{}`，所以直接拿`hope{`和密文爆出了key。

### crypto/kfb
观察加密脚本。
``` python
def encrypt(k, pt):
  assert len(k) == BLOCK
  pt = pad(pt, BLOCK)
  ct = b''
  for bk in ichunked(pt, BLOCK):
    ct += strxor(encrypt_block(k, k), bytes(bk))
  return ct
```
可以看到`encrypt_block出来的`每次都是同一个东西，记为K。  
题目允许你自己提供一个hex上去，返回加密的内容。  
记flag为flag，flag密文为a，提供的内容为m，提供内容的密文为b。  
有：flag ^ K = a, m ^ k = b  
则：flag = a ^ b ^ m。  
实际解题时要注意xor的方式，和BLOCK大小（有一个padding）。

---
*未完待续*
