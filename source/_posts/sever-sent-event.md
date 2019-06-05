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

我的第一个困惑就是上边的一段代码并没有任何地方定义了链接时间，从逻辑上并看不出来server多久会给client发送新的消息。也不知道是什么导致了信息从server发送到client。

然而体感告诉我Client每隔3秒钟就会受到一条新的消息。原来Sever的config里还有玄机。这里省略了一个message field `retry` 如果缺省就会被自动定义为 3 秒。而这就是connection close之后重连的时间。

那么如何让connection变成persistent从而使得我们可以编写一些逻辑按照我们的需求trigger data的发送呢。



