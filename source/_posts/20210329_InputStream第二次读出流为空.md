---
title: InputStream第二次读出流为空
description: inputStream 只能读一次，要多次读取需要先转成byte
date: 2021-03-29 22:54:11
tags:
- Java
categories: 
- Technology
---

## 起因

在读取文件流的时候，想再一次获取InputStream，但实际却获取了null，导致了写入了一个空文件。

***

## 解决

```java
//获取客户端InputStream对象
InputStream input= requset.getInputStream();
//将InputStream对象转换成ByteArrayOutputStream
ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();  
byte[] buffer = new byte[1024];  
int len;  
while ((len = input.read(buffer)) > -1 ) {  
    byteArrayOutputStream.write(buffer, 0, len);  
}  
byteArrayOutputStream.flush();                
//将byteArrayOutputStream可转换成多个InputStream对象，达到多次读取InputStream效果
InputStream inputStreamA = new ByteArrayInputStream(byteArrayOutputStream.toByteArray());
InputStream inputStreamB = new ByteArrayInputStream(byteArrayOutputStream.toByteArray()); 
//将InputStream转换成字符串
BufferedReader br = new BufferedReader(new InputStreamReader(inputStreamB,"UTF-8"));
String line = null;
StringBuilder sb = new StringBuilder();
while ((line = br.readLine()) != null) {
    sb.append(line);
}
```

***

## 原因

读取文件流时，就是将InpuStream从开始读成二进制，但是这个流是不可逆的，也就是读一次就读到了文件流的末尾。所以在下一次需要读文件流时，其实只从文件流的末尾开始读，当然也就是个空，什么也没有读出来。
先将InputStream读到一个StringBuffer是比较好的选择。
