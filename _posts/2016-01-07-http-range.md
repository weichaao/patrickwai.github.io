---
layout: post
title: "HTTP RANGE"
description: ""
category: android
tags: []
---
#断点续传

##Range
Range的规范定义：
<pre>
    ranges-specifier = byte-ranges-specifier
    byte-ranges-specifier = bytes-unit "=" byte-range-set
    byte-range-set  = 1#( byte-range-spec | suffix-byte-range-spec )
    byte-range-spec = first-byte-pos "-" [last-byte-pos]
    first-byte-pos  = 1*DIGIT
    last-byte-pos   = 1*DIGIT
</pre>

byte-range-set是byte-range的一个集合，根据集合的定义，实际请求中完全可能出现这种情况：
Range:bytes=123-567,600-699,789-。数据区间是闭合的，缺省的起始值为0。

在byte-range-spec里，last-byte-pos是可以省略的，表示从first-byte-post到最后一个字节位置

suffix-byte-range-spec实际上就是省略了first-type-pos的byte-range-spec，表示形式为
"-299"，意思是从开头到第299个字节，也即是开头的300个字节。

示例：

Range: bytes=123-567,789-

Range: bytes=0-499, 表示请求文件开头的500个字节，0-499是一个闭合区间

Range: bytes=-200, 表示请求文件结尾的200个字节

如果first-byte-pos大于last-byte-pos，那么这个Range请求就是无效的，服务器需要忽略这个请求，并返回200 OK，
然后把整个文件发送给客户端。如果first-byte-pos大于文件长度，或者suffix-byte-range-spec的suffix-length等于0，
那么这个Range请求被认为是不能满足的，server需要回应`416 Requested range not satisfiable`。




##Content-Rnage

server 除了要能理解 Range 请求之外，在回应 client 时还要使用 Content-Range 来告诉 client 他到底
发送了多少数据，Content-Range 的规范定义如下：
<pre>
    Content-Range = “Content-Range” “:” content-range-spec
    content-range-spec = byte-content-range-spec
    byte-content-range-spec = bytes-unit SP byte-range-resp-spec “/” ( instance-length | “*” )
    byte-range-resp-spec = (first-byte-pos “-” last-byte-pos) | “*”
    instance-length = 1*DIGIT
</pre>
* SP表示空格

举个例子说明一下，比如某文件的大小是 1000 字节，client 请求这个文件时用了 “Range: bytes=0-500”，
那么server应该把这个文件开头的501个字节发回给client，同时回应头要有如下内容：
Content-Range: bytes 0-500/1000

注意: 
* 不支持Range请求的服务器要用"Accept-Ranges:none"对客户端表明心意。
  server可以主动告诉client "Accept-Ranges: bytes"，
  但是client可以在没有收到这个指示的前提下向server发送Range请求。
* byte-range-set 中的区间可以是“有洞”的，也可以是部分重叠的
* 单区间的 byte-range-set 正常回应就可以了，但是多区间 byte-range-set 需要
  server 使用 multipart/byterange 来回应
  
