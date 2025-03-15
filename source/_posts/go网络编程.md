---
title: go网络编程
date: 2024-7-24 14:00:00
update: 2024-7-24 8:25:00
tags: C++
cover: https://images5.alphacoders.com/124/thumbbig-1240551.webp
---

## 启动一个简单的http服务
``` go
package main

import (
	"net/http"
)

func main() {
	http.HandleFunc("/ping", func(w http.ResponseWriter, r *http.Request) {
		w.Write([]byte("pong"))
	})

	http.ListenAndServe(":8091", nil)
}
```

&emsp;&emsp;调用http.HandleFunc方法，注册对应请求路径下的处理函数,调用http.ListenAndServe方法，启动端口号为8091的http服务。

&emsp;&emsp;在浏览器输入http://localhost:8091/ping后，可以看到页面上的pong