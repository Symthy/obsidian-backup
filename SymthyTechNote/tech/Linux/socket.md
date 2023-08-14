
sockファイル 
- = UNIX ドメイン ソケット？
- TCP ソケットとは違う？
- ローカルでの通信を sockファイルへの入出力で行うらしい（ググって見つけたものより）

>ソケットファイルと呼ばれる特殊なファイルを用いたソケット通信（Unixドメインソケット通信）
  ref: [今更ながらソケット通信に入門する（Pythonによる実装例付き）](https://qiita.com/t_katsumura/items/a83431671a41d9b6358f)


>The socket file has all permissions enabled
 ref: [LinuxJM - UNIX](http://linuxjm.osdn.jp/html/LDP_man-pages/man7/unix.7.html)



## ソケット作成/接続

nc (netcat) コマンドできる

ソケットファイル作成
```sh
nc -l -U /path/xxx.sock
```

接続
```
nc -Uv /path/xxx.sock
```

ソケット一覧確認
```sh
netstat -al --protocol=unix
```

## 参考

>TCPとUDPによるソケット通信は、外部のネットワークに繋がるインタフェースに接続します。 これに対し、Unixドメインソケットでは外部インタフェースへの接続は行いません。 その代わり、カーネル内部で完結する高速なネットワークインタフェースを作成します。
> ref: [Unixドメインソケット](https://ascii.jp/elem/000/001/415/1415088/)

[「UNIX ドメインソケット」と「ソケット」について比較する](https://qiita.com/toshihirock/items/b643ed0edd30e6fd1f14)

[Webサーバにおけるソケット周りの知識](https://christina04.hatenablog.com/entry/socket-base)

[unixドメインソケット + golang](https://www.sunapro.com/unix-domain-socket/)

[TCP/IPとは？通信プロトコルの階層モデルを図解で解説](https://www.itmanage.co.jp/column/tcp-ip-protocol/#:~:text=TCP%2FIP%E3%81%AE%E3%82%BD%E3%82%B1%E3%83%83%E3%83%88%E9%80%9A%E4%BF%A1%E3%81%A8%E3%81%AF%E3%80%81%E3%82%BD%E3%82%B1%E3%83%83%E3%83%88%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%8D%E3%83%83%E3%83%88%E3%81%A8%E3%82%82,%E7%94%A8%E3%81%84%E3%81%A6%E9%96%8B%E7%99%BA%E3%81%97%E3%81%BE%E3%81%99%E3%80%82)