

ref: https://oohira.github.io/gobyexample-jp/base64-encoding.html

```go
package main

import (
    b64 "encoding/base64"
    "fmt"
    "strings"
)

const jwt = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmbGFnIjoiZjFuYXQzeHRoZHs4MDExODE0Yi00NzBjLTRmM2MtYWE2ZS0yMTVhYThkYjk5MjR9In0.-LoKWOcI9J1xbnul1YziIxjUziIIS8jZMfpB1HPx4BI"

func main() {
    payload := strings.Split(jwt, ".")[1]
    decodedPayload, _ := b64.StdEncoding.DecodeString(payload)
    fmt.Println(string(decodedPayload))
}
```