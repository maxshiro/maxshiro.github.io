---
layout: wiki
wiki: dvwa
breadcrumb: false
title: 什么是命令注入？
order: 101
comments: false
---

## 什么是命令注入？

命令注入就是在需要输入数据的地方输入了恶意代码，而且系统并没有对其进行过滤或者其他处理导致恶意代码也被执行，最终导致数据泄露或者正常数据被破坏。

## 常用到的命令

>（总结来说就是系统操作命令DOS命令都可以在此执行试试）

ipconfig，net user（查看系统用户），dir（查看当前目录），find（查找包含指定字符的行），whoami（查看系统当前有效用户名）A&B（简单的拼接，AB之间无制约关系），A&&B（A执行成功才会执行B），A|B（A的输出作为B的输入），A||B（A执行失败，然后才会执行B）

## Low

### 分析
根据提示，我们先随便输入一个IP提交下试试。

![](/img/index/2023-06-21-19-45-18.png)

下面出现了PING的过程  
如果你熟悉linux的操作的话你一定知道有些语法可以让你在一行内运行多条命令：

- `;` 
分号，没有任何逻辑关系的连接符。当多个命令用分号连接时，各命令之间的执行成功与否彼此没有任何影响，都会一条一条执行下去。
- `||`
逻辑或，当用此连接符连接多个命令时，前面的命令执行成功，则后面的命令不会执行。前面的命令执行失败，后面的命令才会执行。
- `&&`
逻辑与，当用此连接符连接多个命令时，前面的命令执行成功，才会执行后面的命令，前面的命令执行失败，后面的命令不会执行，与 || 正好相反。
- `|`
管道符，当用此连接符连接多个命令时，前面命令执行的正确输出，会交给后面的命令继续处理。若前面的命令执行失败，则会报错，若后面的命令无法处理前面命令的输出，也会报错。

我们使用任意一条构建payload：
```
127.0.0.1 | pwd
```
![](/img/index/2023-06-21-19-55-25.png)
发现已经注入成功了..

### 查看源代码

{% folding 点我展开 open:false %}
```php

<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $target = $_REQUEST[ 'ip' ];

    // Determine OS and execute the ping command.
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
        // Windows
        $cmd = shell_exec( 'ping  ' . $target );
    }
    else {
        // *nix
        $cmd = shell_exec( 'ping  -c 4 ' . $target );
    }

    // Feedback for the end user
    echo "<pre>{$cmd}</pre>";
}

?>
```
{% endfolding %}

我们可以发现初级并没有使用任何字符过滤，这也就导致了命令注入的成功执行。。。

## Medium

我们直接使用刚才的payload试试:

![](/img/index/2023-06-21-20-06-42.png)

竟然直接成功了。。。
我们看看源代码

{% folding 点我展开 open:false %}
```php
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $target = $_REQUEST[ 'ip' ];

    // Set blacklist
    $substitutions = array(
        '&&' => '',
        ';'  => '',
    );

    // Remove any of the charactars in the array (blacklist).
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );

    // Determine OS and execute the ping command.
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
        // Windows
        $cmd = shell_exec( 'ping  ' . $target );
    }
    else {
        // *nix
        $cmd = shell_exec( 'ping  -c 4 ' . $target );
    }

    // Feedback for the end user
    echo "<pre>{$cmd}</pre>";
}

?>
```
{% endfolding %}

他只是单纯的加了`&&`和`;`的过滤。。。
## High
按照惯例，我们继续使用老的payload试试：
![](/img/index/2023-06-21-20-10-23.png)

并没有输出。。

我们直接看看源代码
{% folding 点我展开 open:false %}
```php
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Get input
    $target = trim($_REQUEST[ 'ip' ]);

    // Set blacklist
    $substitutions = array(
        '&'  => '',
        ';'  => '',
        '| ' => '',
        '-'  => '',
        '$'  => '',
        '('  => '',
        ')'  => '',
        '`'  => '',
        '||' => '',
    );

    // Remove any of the characters in the array (blacklist).
    $target = str_replace( array_keys( $substitutions ), $substitutions, $target );

    // Determine OS and execute the ping command.
    if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
        // Windows
        $cmd = shell_exec( 'ping  ' . $target );
    }
    else {
        // *nix
        $cmd = shell_exec( 'ping  -c 4 ' . $target );
    }

    // Feedback for the end user
    echo "<pre>{$cmd}</pre>";
}

?>
```
{% endfolding %}

发现好多都被匹配到了，我们别慌

构造payload
```
127.0.0.1 || pwd
```

![](/img/index/2023-06-21-20-15-13.png)

发现已经可以了。

为什么可以成功执行呢？

我们看看他匹配的部分，前后都没有空格，而我们构造的有空格，所以就没有匹配上。。。

## impossible
最后一关了，每每到这一关是不是心情大好，因为这一关就是告诉你怎么防御这些漏洞的。
{% folding 点我展开 open:false %}
```php
<?php

if( isset( $_POST[ 'Submit' ]  ) ) {
    // Check Anti-CSRF token
    checkToken( $_REQUEST[ 'user_token' ], $_SESSION[ 'session_token' ], 'index.php' );

    // Get input
    $target = $_REQUEST[ 'ip' ];
    $target = stripslashes( $target );

    // Split the IP into 4 octects
    $octet = explode( ".", $target );

    // Check IF each octet is an integer
    if( ( is_numeric( $octet[0] ) ) && ( is_numeric( $octet[1] ) ) && ( is_numeric( $octet[2] ) ) && ( is_numeric( $octet[3] ) ) && ( sizeof( $octet ) == 4 ) ) {
        // If all 4 octets are int's put the IP back together.
        $target = $octet[0] . '.' . $octet[1] . '.' . $octet[2] . '.' . $octet[3];

        // Determine OS and execute the ping command.
        if( stristr( php_uname( 's' ), 'Windows NT' ) ) {
            // Windows
            $cmd = shell_exec( 'ping  ' . $target );
        }
        else {
            // *nix
            $cmd = shell_exec( 'ping  -c 4 ' . $target );
        }

        // Feedback for the end user
        echo "<pre>{$cmd}</pre>";
    }
    else {
        // Ops. Let the user name theres a mistake
        echo '<pre>ERROR: You have entered an invalid IP.</pre>';
    }
}

// Generate Anti-CSRF token
generateSessionToken();

?>
```
{% endfolding %}

查看源代码我们发现了问题

程序使用了正则匹配的方式来禁止输入其他字符，这样我们就没法使用linux命令的特性来攻击了。

同时也设置了token，这个是用来防止CSRF的，后面我会提到。

至此，关于命令注入的就全部结束了，DVWA上面确实很简单。