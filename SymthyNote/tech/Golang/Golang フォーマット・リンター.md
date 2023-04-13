
## フォーマット
結論：go fmt + goimports

・go fmt：標準
 
・**go imports**：強化版 フォーマット＋import文をクリーンにできる
```
go install golang.org/x/tools/cmd/goimports@lastest
```
-l ：フォーマットが正しくないファイルをコンソールに表示
-w：ファイルを直接置き換える
※第三引数はディレクトリ指定
```
goimports -l -w .
```

## リンター
結論：staticcheck + go vet  慣れてきたら golangci-lint

Effective Go や Go Code Review Comments のようなイディオム的なGoコードのスタイルを徹底させてくれるツール

・staticcheck：リンター
```
golang honnef.co/go/tools/cmd/staticcheck@lastest
```
・go vet：潜在的なバグを見つける

・golangci-lint：staticcheck + go vet それ以外にもいくつかのリンターを実行する


#golang
