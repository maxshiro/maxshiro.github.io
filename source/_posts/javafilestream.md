---
title: Java中的文件流读写
categories: [languages, java]
date: 2023-05-09 20:17:44
tags: 
- java
- filestream
cover:
banner:
---
## 一个简单的写入文件并读取文件程序。
```java java
public class filestream {
    // 主函数，使用psvm自动补全， throws IOException ：如果在该方法中调用了可能抛出已检查异常的方法，比如文件读写操作，就需要在方法声明中加上throws子句来声明可能抛出的异常。
    public static void main(String[] args) throws IOException {
        // 使用 File 指定文件目录为一个变量“f”
        File f=new File("D:\\Project\\Java\\test.txt");
        // 创建一个字节输出流 FOS 对象 fop，使用fop.close();控制输出流关闭.
        // FileOutputStream(使用File指定的文件路径变量, 是否保留源文件)；
        FileOutputStream fop=new FileOutputStream(f, true);
        // 创建一个OSW对象（OutputStreamWriter是一个字符输出流，它将字节流转换为字符流。）writer，使用write.append("内容");来输出流到文件。
        // OutputStreamWriter(字节输出流变量, “编码”);
        OutputStreamWriter writer = new OutputStreamWriter(fop, "UTF-8");
        // 使用OSW对象变量名.append("内容");来输出流到文件。
        writer.append("中文测试");
        // 换行
        writer.append("\n\r");
        writer.append("English");
        writer.close();
        // 关闭输出流
        fop.close();
        // 创建一个字节输入（读取）流 FIS 对象 fip，使用fip.close();控制输出流关闭.
        // FileInputStream(使用File指定的文件路径变量)；
        FileInputStream fip = new FileInputStream(f);
        // 创建一个ISR对象 (InputStreamReader 是一个字符输入流，它将字节流转换为字符流。) reader
        // reader.ready()读取流是否准备好。
        // reader.read()读取
        // OutputStreamWriter(字节输出流变量, “编码”);
        InputStreamReader reader = new InputStreamReader(fip, "UTF-8");
        // 创建字符缓存 sb，用他来读取文件一行内容到缓存中。
        StringBuffer sb = new StringBuffer();
        // 程序使用一个 while 循环读取输入流中的字符，并将其追加到 sb 中，直到输入流中没有字符可读。在每次读取时，程序使用 (char) reader.read() 将读取到的字节转换为字符，并将其追加到 sb 中。
        while (reader.ready()){
            sb.append((char) reader.read());
        }
        // 输出。
        System.out.println(sb.toString());
        // 关闭输入输出流。
        reader.close();
        fip.close();
    }
}
```

