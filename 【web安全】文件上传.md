##### 客户端验证

- 禁用 js 。
- 删除表单部分的 onsubmit 。

##### MIME类型检测

&emsp;&emsp;抓包，改 Content-Type ，建议改为 image/gif，方便打组合拳。

##### 扩展名黑名单

###### Windows特点

&emsp;&emsp;需要 php 脚本运行在 Windows 上。

- 对大小写不敏感
  * 限制：php-版本号-nts+Apache 无法解析。
- 自动去除扩展名末尾的空格、点、::$DATA

###### PHP特点

- 5.2.x 版本存在 00 截断漏洞。
- user.ini 可覆盖 php.ini
- move_upload_file 函数会忽略扩展名末尾的 /.
  * 绕过：抓包，依此修改扩展名。

###### Apache特点

- .phtml、.php2、.php3、.php5、.pht 等可做为 .php 解析。
  
- .htaccess 文件可覆盖配置文件

##### 文件内容检测

###### 图片马绕过

- 制作：执行命令 `copy/b 图片名+文件名 图片马名` 。

###### 文件幻术绕过

- 制作：在 `<?php ?>` 前添加文件幻术 ，并将 .php 改为对应扩展名。  

###### NOTICE

- 只检测文件头（前2个字节）：文件幻术、图片马均可突破。
- 使用 getimagesize 函数：需图片马。
- 使用 exif_imagetype 函数：需图片马。

##### 代码漏洞

###### 条件竞争

###### 二次渲染

###### 其他

##### 解析漏洞

&emsp;&emsp;参考[这篇文章](https://www.anquanke.com/post/id/219107)，待补。

###### IIS5.x-6.x

&emsp;&emsp;使用 IIS5.x-6.x 版本服务器的网站一般都比较古老，开发语言一般为 asp ,该解析漏洞也只能解析 .asp 文件。

- 如果网站目录中有一个 xxx.asp/ 的文件夹，那么该文件夹下的所有文件都会被当作 .asp 文件来执行。
- 解析文件名时会将分号后面的内容丢弃，所以可构造如 xxx.asp;.jpg 来绕过黑名单检测。

###### PHP CGI

&emsp;&emsp;以例说明，访问 xxx/a.jpg 时，在后面加上 /xxx.php（即xxx/a.jpg/xxx.php），将把 a.jpg 做为 php 文件解析。

&emsp;&emsp;存在于 IIS7.0/7.5、Nigix 服务器中，且需 php.ini 中 cgi.fix_pathinfo 选项为 1（默认为1）。

###### Apache

&emsp;&emsp;Apache 在解析扩展名时是从右向左，如果遇到不能识别的扩展名则跳过，像 .rar、.gif 是 Apache 不能识别的。

&emsp;&emsp;假如上传文件 xxx.php.bb.rar，.rar 不认识，向前解析，.bb 也不认识，向前解析，直到 .php 。

&emsp;&emsp;影响版本：

- Apache 2.0.x <= 2.0.59
- Apache 2.2.x <= 2.2.17
- Apache 2.2.2 <= 2.2.8