---
title: Sever Sent Event
date: 2019-06-05 15:01:45
tags: 
 - coding
 - web
---

最近做了一个需要实现实时通信的项目，在尝试中了解到了SSE，感觉很有意思遂抽空把学习实践过程中遇到的问题记录下来。

# 实现逻辑

先看一段最基础的代码：

Sever side(PHP) <<BestLangintheWorld:

```
<?php

header("Cache-Control: no-cache");
header("Content-Type: text/event-stream\n\n");

$time = date("r");

echo "data: The Server Time has Changed {$timr}\n";

flush();

?>
```

Client Side(JS):

```
if (typeof(EventSource) != "undefined") {
    var source = new EventSource("path/to/server_side.php");
    source.onmessage = function(event) {
        document.gerElementById("result").innerHtml += event.data + <br>";
    };
} else {
    console.log("SSE not supported");
};
```

我的第一个困惑就是上边的一段代码并没有任何地方定义了链接时间，从逻辑上并看不出来 server 多久会给 client 发送新的消息。也不知道是什么导致了信息从 server 发送到 client。

然而体感告诉我 Client 每隔3秒钟就会受到一条新的消息。原来 Sever 的 config 里还有玄机。这里省略了一个 message field `retry` 如果缺省就会被自动定义为 3 秒。而这就是 connection close 之后重连的时间。

那么如何让 connection 变成 persistent 从而使得我们可以编写一些逻辑按照我们的需求 trigger data的发送呢。

原来每一个 connection 都与一个 php instance binding，也就是说上边的 server side 代码执行完之后自然连接就断开了。那么我们只要在php代码外边包一层 loop 就可以保持 connection 一直处于开启状态。

下一步自然是处理 new message 的逻辑。我们知道了现在每个 client 都有一个独立的 php instance 服务，如何让这些 instance 识别新消息的到来并且把消息发送到 client 呢？

在网上查阅了很多资料之后感觉传统的 php 网站架构并不适合解决这种问题，当然有一些诸如 shared session 或者 global variable 邪道可以做到，但是这些无疑都是很差的代码习惯。我们也可以尝试使用 socket 来监控新数据的写入或者用server端的一个 shared flat file 来记录。但是在我看来都是非常不好的代码。。。

比起使用 php + SSE 的老爷爷组合，event orientated 架构如 Node.js 更适合这种工作。

但是公司要用 php 我也无能为力呀。接下来我打算用一个 record db 来记录每次的 data 变动操作。在 SSE 代码里做一个 event loop 每次 check record db，有更新的话才发送完整的数据到 client 否则只发送一个代表状态的短消息，这样可以有效减少带宽占用。当然这样做会有 1s 的延迟（depend on event loop 的写法。。。）但是对于现在的用户量和功能性来讲完全是可以容忍的了。只是与我最开始的设想相去甚远。Anyway 来做一下，看看还会不会遇到别的问题。

其实想实现这个项目里面的功能 websocket 无疑是一个更好的选择。这个协议是 full-duplex 的，功能性更全面，它的通讯实现是基于TCP协议。而 SSE 更适合于搭建 server 单向 client 传输数据这种系统，实现起来十分简单和简洁。SSE 是基于 http 实现的。注意，很多浏览器对于 SSE 最大可以建立的链接数量有很强的限制，一般都是 6。也就是一台机器最多只能支持 6 个 SSE 同时开启。这是在搭建系统是值得考虑的事情。

最终第一版系统使用 SSE 搭建，并且可以上线使用了。接下来如果有时间会考虑使用 websocket 重构整个代码。
