---
layout: wiki
wiki: dvwa
breadcrumb: false
title: 存储型XSS
order: 202
comments: false
---
XSS存储型攻击，攻击者事先将恶意代码上传或储存到漏洞服务器中，只要受害者浏览包含此恶意代码的页面就会执行恶意代码。这就意味着只要访问了这个页面的访客，都有可能会执行这段恶意脚本，因此储存型XSS的危害会更大。因为存储型XSS的代码存在于网页的代码中，可以说是永久型的。

存储型 XSS 一般出现在网站留言、评论、博客日志等交互处，恶意脚本存储到客户端或者服务端的数据库中。

## Low-level
![](/img/store/2023-06-24-20-28-31.png)
如图，有两个输入框，我们随便输入点内容：
![](/img/store/2023-06-24-20-29-13.png)
发现他会写入到下面。

我们构造payload：
```payload
name: test
message: <script>alert(1)</script>
```
![](/img/store/2023-06-24-20-35-50.png)

过了。看看源代码：

{% folding open:false %}
```php
<?php

if( isset( $_POST[ 'btnSign' ] ) ) {
    // Get input
    $message = trim( $_POST[ 'mtxMessage' ] );
    $name    = trim( $_POST[ 'txtName' ] );

    // Sanitize message input
    $message = stripslashes( $message );
    $message = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $message ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Sanitize name input
    $name = ((isset($GLOBALS["___mysqli_ston"]) && is_object($GLOBALS["___mysqli_ston"])) ? mysqli_real_escape_string($GLOBALS["___mysqli_ston"],  $name ) : ((trigger_error("[MySQLConverterToo] Fix the mysql_escape_string() call! This code does not work.", E_USER_ERROR)) ? "" : ""));

    // Update database
    $query  = "INSERT INTO guestbook ( comment, name ) VALUES ( '$message', '$name' );";
    $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

    //mysql_close();
}

?>
```
{% endfolding %}


![](/img/store/2023-06-24-20-30-35.png)

特殊符号全部给转译了。

我们尝试在Name上构建Payload：
![](/img/store/2023-06-24-20-31-27.png)
发现有字数限制。尝试打开检查看看源代码。

右键name框选择检查。

![](/img/store/2023-06-24-20-32-55.png)

如图，果然有MAXlength，直接进行一个更改：
![](/img/store/2023-06-24-20-33-29.png)
反写payload，尝试：
![](/img/store/2023-06-24-20-34-18.png)

