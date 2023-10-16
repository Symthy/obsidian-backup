
以下でバージョン指定はできる

```
client := &http.Client{
    Transport: &http.Transport{
        TLSClientConfig: &tls.Confg{
            MinVersion: tls.VersionTLS12,
            MaxVersion: tls.VersionTLS13,
        }
    }
}
```



refs: 
- [Go の HTTP クライアントで mTLS を利用する](https://zenn.dev/shiguredo/articles/go-mtls-cloudflare-mtls)
- [golang で HTTP3 を試してみる](https://zenn.dev/satoken/articles/golang-hajimete-http3)
- [Go: http.Client に独自ルート証明書を追加する](https://qiita.com/frozenbonito/items/bb615e09dcee3175ef5a)

