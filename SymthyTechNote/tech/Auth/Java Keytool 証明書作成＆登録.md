
keystore 中身確認
```sh
keytool -list -v -storepass changeit -keystore <keystore_path>
```

keystore作成（自己証明書）

```sh
LOCAL_IP_ADDR=`hostname -I | awk '{print $1}'` # マルチIP用に最初だけ取る
keytool -genkey -alias <alias_name> -keyalg RSA -dname"CN=${LOCAL_IP_ADDR}, OU=, O=, L=, ST=, C=" -ext san=dns:<hostname>,ip:${LOCAL_IP_ADDR} -keypass changeit -keystore <keystore_path> -storepass changeit -validity 3652 -storetype PKCS12
```

keystoreから証明書ファイル(pem)へ変換
```sh
openssl pkcs12 -in <keystore_path> -nodes -passin pass:changeit | openssl x509 -out xxx.pem
```

keystoreへ証明書インポート
```sh
keytool -import -alias <alias_name> -file <pem_file_path> -keystore <keystore_path> -storepass changeit -storetype JKS
```

keystore から証明書削除
```sh
keytool -delete -alias <alias_name> -keystore <keystore_path> -storepass changeit
```

keystore内の証明書確認
```sh
openssl pkcs12 -in <keystore_path> -nodes -passin pass:changeit | openssl x509 -noout -fingerprint -text
```

※ JKS から PEM形式の証明書(X.509) に変換するには
JKS → PKCS12 → PEM に変換が必要

[Javaのkeytoolのキーストア（JKS）からPEM形式の証明書、秘密鍵に変換する方法](https://qiita.com/yasushi-jp/items/4aa690fbde11841686e5)
