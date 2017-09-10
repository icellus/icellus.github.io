---
layout: post
title: PHP异步的实现
category: PHP
tags: [PHP]
description: None
---

实际业务中，经常会遇到一个场景：一个很繁琐的操作中需要加入一个耗时长，可以不需要立即获得结果（或者结果并不是很重要）的业务逻辑。

这个时候，可以考虑用异步进程来实现。

不同于ajax，php的实现可能会复杂一点。



#### 具体代码

````php
/**
 * @return bool
 * 异步任务触发机制
 */
public function triggerAsyncTask($host,$port, $targetUrl)
{
	ignore_user_abort(true); // 如果服务器默认没有ignore_user_abort 开启这个并在后面sleep(1)
	$fp = fsockopen($host, $port, $errNo, $errStr, 30);

  	if (!$fp) {
    	// 打开连接失败
    	return false;
  	} else {
    	$out = "GET $targetUrl HTTP/1.1\r\n";
    	$out .= "Host: $host\r\n";
    	$out .= "Connection: Close\r\n\r\n";
    	fwrite($fp, $out);
    	sleep(1);

    	fclose($fp);
    	return true;
  	}
}
````

**然后注册对应的路由并进行调用即可**



### 总结

这种方法或者其他实现的优劣，暂时也不是很明白。

guzzle可是能是一个不错的选择。
