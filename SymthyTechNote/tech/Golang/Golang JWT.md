

refs: 
-  [GoでJWTの具体的な実装](https://christina04.hatenablog.com/entry/2017/04/15/042646)
- [Go を使って JWt を15分で理解する](https://taisablog.com/archives/go-jwt)

## jwt の検証 (jwt と 公開鍵による検証)

```go
package jwtoken

import (
    "errors"
    "fmt"
  
    "github.com/golang-jwt/jwt/v5"
)

func Validate(tokenString, publicKey string) (*jwt.Token, error) {
    parsedToken, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
        return publicKey, nil
    })
    if err != nil {
        return nil, fmt.Errorf("Token is invalid: %w", err)
    }
    if !parsedToken.Valid {
        return nil, errors.New("Token is invalid")
    }
    return parsedToken, nil
}
```


## 秘密鍵を元にしたJWT特定

```go
	splitedJwt := strings.Split(jwt, ".")
	header, payload, signiture := splitedJwt[0], splitedJwt[1], splitedJwt[2]
	unsignedToken := header + "." + payload

	h := sha256.New()
	h.Write([]byte(unsignedToken))
	hashed := h.Sum(nil)

	signedData, err := rsa.SignPKCS1v15(nil, privateKey, crypto.SHA256, hashed)
	if err != nil {
		fmt.Printf("%d: %v\n", i, err)
		continue
	}

	// URLエンコード にする必要がある点に注意
	createdSigniture := base64.URLEncoding.EncodeToString(signedData)
	if strings.Contains(createdSigniture, signiture) {
		decodedPayload, _ := base64.StdEncoding.DecodeString(payload)
		fmt.Printf("%s", decodedPayload)
	}
```