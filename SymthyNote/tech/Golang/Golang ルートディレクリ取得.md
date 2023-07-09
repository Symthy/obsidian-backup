
buildしたバイナリで使うと、ビルド時の環境のパスになる
プロダクトには使えない（ローカルでしか使えない）

```go
package projectpath

import (
    "path/filepath"
    "runtime"
)

var (
    _, b, _, _ = runtime.Caller(0)
    Root = filepath.Join(filepath.Dir(b), "../../../")
)
```