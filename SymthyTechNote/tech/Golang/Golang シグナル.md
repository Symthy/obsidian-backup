
syscall.SIGINT は古い？ 今は os.Intrrupt を使う（情報源なし

- signal.NotifyContext：アプリケーションはレスポンスを返してから終了する
- signal.Notify：アプリケーションはレスポンスも返さずに終了する
```go

func run(ctx context.Context) error {
	ctx, stop := signal.NotifyContext(ctx, os.Interrupt, syscall.SIGTERM)
    defer stop()
    // :
}
```


無期限に実行されるプログラムを止める唯一の方法は、SIGINT を送信すること。

```go
package main

import (
    "os"
    "os/signal"
)

func main() {
	proc := MockProcess{}
	go func() {
		sig := make(chan os.Signal, 1)
		signal.Notify(sig, os.Interrupt)
		<-sig
		signal.Reset() // 元の動きに戻す？
		proc.Stop()
	}
	proc.Run()
}
```
ref: 
- [Go Graceful SIGINT killing](https://enzircle.com/go-graceful-sigint-killing)
- [Goメモ-265 (シグナル通知設定をリセットする)(signal.Reset)](https://devlights.hatenablog.com/entry/2022/11/08/073000)


```go
package main

import (
    "fmt"
    "os"
    "os/signal"
    "syscall"
)

func main() {
    sigs := make(chan os.Signal, 1)
    signal.Notify(sigs, os.Interrupt, syscall.SIGTERM)
    done := make(chan bool, 1)

    go func() {
        sig := <-sigs
        fmt.Println(sig)
        done <- true
    }()

    fmt.Println("awaiting signal")
    <-done
    fmt.Println("exiting")
}
```
ref: [Go by Example: Signals](https://oohira.github.io/gobyexample-jp/signals.html) 


## その他 refs

[[Linux シグナル]]

[シグナルハンドリングとContextを使って安全にGoのプログラムを終了させる](https://blog.kaznishi.com/post/190916_gracefulstop/)

[go言語でシグナルをきちんとエラーハンドリングする](https://ludwig125.hatenablog.com/entry/2019/09/25/053500)

#golang/signal
