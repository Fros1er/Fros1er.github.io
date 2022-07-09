---
title: compass-writeup
date: 2022-07-09 17:29:52
tags: ctf
---

我也没想到我第一篇博客会是ctf...其实之前有很多可以写的东西，但那基本上是高中的时候，我没有稳定的环境（其实就是自己不会）跑hexo（确信）。

总之，目前有在参加[COMPASS](https://wiki.compass.college/)的培训。打不打比赛不知道，主要还是想图一乐加上了解一点点security。然后我需要点地方记笔记，就把这个远古page拿过来用了。

以下是正文，会持续更新。

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

---

*未完待续*