
refs:
- [[Golang] csvを操作するための基本的な知識](https://zenn.dev/syo_yamamoto/articles/1fb502ef862490)
- [GoでCSVのファイルを読み込んで情報を保存する](https://qiita.com/rrih/items/af7f144a62c722e98fb9)
- [Golang で CSV 形式の文字列やファイルを扱う (encoding/csv)](https://maku77.github.io/p/6k5m3iz/)

```go
import (
    "encoding/csv"
    "os"
    "path/filepath"
)

func csvFilePath() string {
    _, b, _, _ := runtime.Caller(0)
    return filepath.Join(filepath.Dir(b), "..", "..", "cmd", "xxx.csv")
}

func main() {
    file, err := os.Open(csvFilePath())
    defer file.Close()
    r := csv.NewReader(file)
}
```