

refs: 
-  [GoでJWTの具体的な実装](https://christina04.hatenablog.com/entry/2017/04/15/042646)

jwt の検証 (jwt と 公開鍵による検証)
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