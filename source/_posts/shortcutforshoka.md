---
title: shortcutforshoka
date: 2022-09-21 12:07:38
hidden: true
tags:
- 短链
- 测试
---

```java java
import java.util.Scanner;
...
Scanner in = new Scanner (System.in);
// 输入 Scan 之后，按下键盘 Alt + “/” 键，Eclipse 下自动补全。
System.out.println (in.nextLine ());
System.out.println ("Hello" + "world.");
```

{% media audio %}
- title: 列表 1
  list:
    - https://music.163.com/#/playlist?id=2943811283
    - https://music.163.com/#/playlist?id=2297706586
- title: 列表 2
  list:
    - https://music.163.com/#/playlist?id=2031842656
{% endmedia %}

原本用于日语汉字假名注音，但别的注音也可以。
为了兼容性，采用 markdown-it-ruby 的基本格式： {文字^注音} ，并且为了兼容表格，将分隔符由 | 换成了 ^ 。
注音分隔基于 furigana-markdown-it 显示说明看这里

{取り返す^とりかえす}	取とり返かえす
{可愛い犬^か+わい・い・いぬ}	可愛かわいい犬いぬ
{可愛い犬^か・わい・いいぬ}	可か愛わいい犬いぬ
{アクセラレータ^accelerator}	アクセラレータaccelerator
{accelerator^アクセラレータ}	acceleratorアクセラレータ
{食べる^たべる}	食たべる
{食べる^=たべる}	食べるたべる
{あいうえお^*}	あ●い●う●え●お●
{あいうえお^*❤}	あ❤い❤う❤え❤お❤
{常用账号^contact}	常用账号contact
