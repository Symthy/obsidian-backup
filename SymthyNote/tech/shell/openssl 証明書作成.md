
共通手順（秘密鍵とcsr:証明書署名要求 作成）
```
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr
```

古い版(多分 X.509 v1)の作成方法
```
openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt
```


X.509 v3 の作成方法
- v3.txt を作成　※keyUsageは制限掛けるものなので無くても簡易な動確では問題ない
```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
```

```
openssl x509 -in server.csr -req -out server.crt -days 3650 -signkey server.key -sha256 -extfile v3.txt
```

[OpenSSLでX.509 v3の証明書を作成する](https://qiita.com/SRsawaguchi/items/f4dc743198e1246fff64)


## refs

- [OpenSSLで自己認証局を作成しServer証明書を作成する方法（概要編）](https://qiita.com/yasushi-jp/items/78c06b1fd3dc107c4961)