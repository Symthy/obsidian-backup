
- ssh-rsa、pem 変換、ssh-rsa -> rsa.PublicKey に変換：
	- https://github.com/takaishi/hello2018/blob/master/ssh_key/README.md
- pem の読み込み～ rsa.PublicKey 生成： [Go で RSA 署名](https://m0t0k1ch1st0ry.com/blog/2014/08/18/rsa-signing/)
- [Go で書くはじめてのデジタル署名](https://qiita.com/keitaj/items/00aede60e64e8eebbb8a)
- [[Go] base64 の４つのエンコード方式の違いを整理する](https://zenn.dev/senk/articles/674e720b6a8700)

- なんか見つけた（使えないが、何かの参考になるかもしれない）
	- https://yosida95.com/2015/05/31/121709.html
	- https://github.com/yosida95/golang-sshkey/blob/master/unmarshal.go#L29


## 公開鍵（pem）読み込み

※pubファイルを直に読み込む方法は不明だが以下で pem にすれば読める
- ssh-keygen で生成された公開鍵
- RFC4716 の形式

```sh
ssh-keygen -f ./test_rsa.pub -e -m pem > test_rsa.pem
```


```go
import (
    "encoding/pem"
    "io/ioutil"
    "strings"
)

func moduleRootDir() string {
    _, b, _, _ := runtime.Caller(0)
    return filepath.Join(filepath.Dir(b), "../")
}

func LoadPemFile(path string) (*pem.Block, error) {
    pemFileData, err := ioutil.ReadFile(path)
    if err != nil {
        return nil, err
    }
    pemBlock, _ := pem.Decode(pemFileData)
    return pemBlock, nil
}

func readPublicKey() (*rsa.PublicKey, error) {
    publicKeyBlock, err := files.LoadPemFile(filepath.Join(moduleRootDir(), "test_rsa.pem"))
    if err != nil {
        return nil, err
    }
    if publicKeyBlock == nil {
        return nil, fmt.Errorf("invalid public key data")
    }
    pub, err := x509.ParsePKCS1PublicKey(publicKeyBlock.Bytes)
    if err != nil {
        return nil, err
    }
    return pub, nil
}
```


## 秘密鍵読み込み

```go
func readPrivateKeyFromPem() (*rsa.PrivateKey, error) {
    privateKeyBlock, err := files.LoadPemFile(filepath.Join(moduleRootDir(), "rsa_private_key.pem"))
    if err != nil {
        return nil, err
    }
    if privateKeyBlock == nil {
        return nil, fmt.Errorf("invalid private key data")
    }

    priv, err := x509.ParsePKCS1PrivateKey(privateKeyBlock.Bytes)
    if err != nil {
        return nil, err
    }
    return priv, nil
}
```

"encoding/pem" を使わずに読み込む
```go
func getPrivateKey() (*rsa.PrivateKey, error) {
    keystr, err := readPrivateKey(filepath.Join(moduleRootDir(), "rsa_private_key.pem"))
    if err != nil {
        return nil, err
    }
    keyBytes, err := base64.StdEncoding.DecodeString(keystr)
    if err != nil {
        return nil, err
    }
    private, err := x509.ParsePKCS1PrivateKey(keyBytes)
    if err != nil {
        return nil, err
    }
    return private, nil
}

func readPrivateKey(filepath string) (string, error) {
    s := ""
    fp, err := os.Open(filepath)
    if err != nil {
        return "", err
    }
    defer fp.Close()

    scanner := bufio.NewScanner(fp)
    for scanner.Scan() {
        text := scanner.Text()
        if text == "-----BEGIN RSA PRIVATE KEY-----" || text == "-----END RSA PRIVATE KEY-----" {
            continue
        }
        s = s + scanner.Text()
    }
    if err := scanner.Err(); err != nil {
        return "", err
    }
    return s, nil
}
```


