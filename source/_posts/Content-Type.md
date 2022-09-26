---
title: Content-Type的常用取值及enctype的区别
date: 2022-09-24 14:31:26
tags:
categories: HTML5
---

## Content-Type

- Content-Type 实体头部用于指示资源的 MIME 类型 media type [¹](#参考文献)。
- 媒体类型（通常称为 Multipurpose Internet Mail Extensions 或 MIME 类型）是一种标准，用来表示文档、文件或字节流的性质和格式。它在 IETF RFC 6838 中进行了定义和标准化 [²](#参考文献)。

### 常用类型

1. application/octet-stream
2. text/plain
3. text/css
4. text/html
5. text/javascript
6. image/\*
7. audio/\*, video/\*
8. multipart/form-data
9. multipart/byteranges

## enctype 取值 [³](#参考文献)

1. application/x-www-form-urlencoded 初始的默认值,窗体数据被编码为名称/值对。这是标准的编码格式。
1. multipart/form-data 窗体数据被编码为一条消息，页上的每个控件对应消息中的一个部分。
1. text/plain 窗体数据以纯文本形式进行编码，其中不含任何控件或格式字符。

- 当 action 为 get 时候，浏览器用 x-www-form-urlencoded 的编码方式把 form 数据转换成一个字串（name1=value1&name2=value2...），然后把这个字串 append 到 url 后面，用?分割，加载这个新的 url; 当 action 为 post 时候，浏览器把 form 数据封装到 http body 中，然后发送到 server。
- 如果没有 type=file 的控件，用默认的 application/x-www-form-urlencoded 就可以了。但是如果有 type=file 的话，就要用到 multipart/form-data 了。浏览器会把整个表单以控件为单位分割，并为每个部分加上 Content-Disposition(form-data 或者 file),Content-Type(默认为 text/plain),name(控件 name)等信息，并加上分割符(boundary)。

### 参考文献

[1] [Content-Type|MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type#:~:text=Content%2DType%20%E5%AE%9E%E4%BD%93%E5%A4%B4%E9%83%A8%E7%94%A8%E4%BA%8E%E6%8C%87%E7%A4%BA%E8%B5%84%E6%BA%90%E7%9A%84%20MIME%20%E7%B1%BB%E5%9E%8B%20media%20type%20%E3%80%82)
[2] [MIME 类型|MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Basics_of_HTTP/MIME_types#:~:text=%E5%85%B6%E4%BB%96-,MIME%20%E7%B1%BB%E5%9E%8B,-%E5%AA%92%E4%BD%93%E7%B1%BB%E5%9E%8B%EF%BC%88%E9%80%9A%E5%B8%B8)
[3] [postman 中 form-data、x-www-form-urlencoded、raw、binary 的区别|掘金](https://juejin.cn/post/6844903826974048264)
