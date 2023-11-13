
## 確認

```sh
openssl x509 -text -noout -in <cert_file>
```

## 作成

共通手順（秘密鍵とcsr:証明書署名要求 作成）
```
openssl genrsa -out server.key 2048
openssl req -new -key server.key -out server.csr
```

古い版(多分 X.509 v1)の作成方法
```
openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt
```



### X.509 v3 の作成方法

v3.txt を作成　※keyUsageは制限掛けるものなので無くても簡易な動確では問題ない
```
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
```

```
openssl x509 -in server.csr -req -out server.crt -days 3650 -signkey server.key -sha256 -extfile v3.txt
```

ref: [OpenSSLでX.509 v3の証明書を作成する](https://qiita.com/SRsawaguchi/items/f4dc743198e1246fff64)

#### SANを設定したいなら以下
v3.cnf を作成
```
[v3_req]
subjectAltName = @alt_names
[alt_names]
DNS.1 = <ドメイン名>
DNS.2 = <ドメイン名>
IP.1 = <IPアドレス>
```

```
openssl x509 -in server.csr -req -out server.crt -days 3650 -signkey server.key -extensions v3_req -extfile v3.cnf
```

ref:
- [Certificate has no `subjectAltName` (証明書にsubjectAltNameがありません)](https://docs.oracle.com/cd/E77565_01/E66514/html/ceph-issues-24424028.html)
- [opensslでマルチドメイン証明書証明書用のCSRを作成](https://rms.ne.jp/sslserver/csr/openssl-html/)
## refs

- [OpenSSLで自己認証局を作成しServer証明書を作成する方法（概要編）](https://qiita.com/yasushi-jp/items/78c06b1fd3dc107c4961)