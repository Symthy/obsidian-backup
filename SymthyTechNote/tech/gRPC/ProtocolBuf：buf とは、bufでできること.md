
## buf とは

以下が可能
-   protoファイルのチェック
    -   lint
    -   フォーマット
    -   破壊的変更のチェック
        -   Protocol Buffersを変更した際に前方・[後方互換](http://d.hatena.ne.jp/keyword/%B8%E5%CA%FD%B8%DF%B4%B9)性を保っているかのチェック
-  BSR への登録/更新

[Protocol Buffersの定義ファイルのチェックはBuf一択でよいのでは？](https://engineer.retty.me/entry/2022/12/05/103000)

※ .proto のフォーマットに clang-format は古い
[.proto ファイルの整形に clang-format を使う](https://christina04.hatenablog.com/entry/protobuf-formatter)
[VSCodeでprotoファイルのフォーマットをする](https://zenn.dev/y16ra/articles/2a88926f71a744)

## インストール

https://buf.build/docs/installation/

Windowsの場合：powershell 

```
PS C:\Users\sym> Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
PS C:\Users\sym> irm get.scoop.sh | iex
Initializing...
Downloading ...
Creating shim...
Adding ~\scoop\shims to your path.
Scoop was installed successfully!
Type 'scoop help' for instructions.

PS C:\Users\sym> scoop install buf
Installing 'buf' (1.15.1) [64bit] from main bucket
buf-Windows-x86_64.exe (24.8 MB) [============================================================================] 100%
Checking hash of buf-Windows-x86_64.exe ... ok.
Linking ~\scoop\apps\buf\current => ~\scoop\apps\buf\1.15.1
Creating shim for 'buf'.
'buf' (1.15.1) was installed successfully!
PS C:\Users\sym>
```

## 初期設定＆各種設定ファイル

モジュールの初期化　※buf.yaml が自動作成される
```bash
 buf mod init
```

更新　※ buf.lock が自動生成/更新される
```
buf mod update
```

buf.gen.yaml と buf.work.yaml を作成

### 設定ファイル

- buf.yaml：モジュールの定義。名前、依存関係、lintやbreakingの設定の定義
	- buf mod init で作成。後は手で更新
	- https://buf.build/docs/configuration/v1/buf-yaml/
- buf.lock：モジュールの依存関係マニフェスト
	- buf mod update で更新（手では触らない？）
- buf.gen.yaml：buf generateコマンドで直接動作するローカルプラグインテンプレートの定義
	- 手で作成
	- https://buf.build/docs/configuration/v1/buf-gen-yaml/
- buf.work.yaml：高度なローカル開発機能であるワークスペースの定義
	- 手で作成
	- https://buf.build/docs/configuration/v1/buf-work-yaml/

### フォルダ構成例
```sh
.
├── buf.work.yaml
├── buf.gen.yaml
├── paymentapis
│   ├── acme
│   │   └── payment
│   │       └── v2
│   │           └── payment.proto
│   └── buf.yaml
└── petapis
    ├── acme
    │   └── pet
    │       └── v1
    │           └── pet.proto
    └── buf.yaml
```

```
.
├── buf.work.yaml
├── buf.gen.yaml
├── proto/
│   ├── buf.md
│   ├── buf.yaml
│   ├── google
│   │   └── type
│   │       └── datetime.proto
│   └── pet
│       └── v1
│           └── pet.proto
```

## buf でできること

サブコマンド一覧

https://buf.build/docs/reference/cli/buf/

### 破壊的変更検出

Protobuf スキーマの以前のバージョンを現在のバージョンと比較する

設定は buf.yaml の breaking に行う。常に設定を行うこと推奨されている： https://buf.build/docs/breaking/overview/

- git の main ブランチとの比較（buf.yaml がルートディレクトリ直下にある前提）
```bash
 buf breaking --against '.git#branch=main'
```

- git のタグを指定しての比較
```bash
 buf breaking --against '.git#tag=v1.0.0'
```

- buf.yaml が サブディレクトリ(proto) にある場合
```bash
buf breaking --against '.git#tag=v1.0.0,subdir=proto'
```

- 特定のファイルのみ比較（複数ファイル指定可能）
```bash
 buf breaking --against '.git#branch=main' --path path/to/foo.proto --path path/to/bar.proto
```

- git の リモートパスでの比較（CI時など上記が使えない時に使える）
```bash
buf breaking --against 'https://github.com/foo/bar.git'
```
※ Github Actions での利用：https://buf.build/docs/ci-cd/github-actions/#buf-setup

docker での実行や、`--config` オプションでの設定上書き実行等もできる

ref: https://buf.build/docs/breaking/usage/

### Lint ＆ Format

- Lint　※出力形式はオプションで変更できる
```bash
 buf lint
```
対象ファイルを絞れる
```bash
buf lint \
  --path path/to/foo.proto \
  --path path/to/bar.proto
```

- Format
```bash
buf format -w
```
※ -w は ファイルを上書きする指定

### コード生成

buf.gen.yaml が必要
```yaml
version: v1
plugins:
  - plugin: buf.build/protocolbuffers/go  # buf のリモートプラグイン(最新)を使用
#  - plugin: buf.build/protocolbuffers/go:v1.28.1  # リモートのプラグインを使用
#  - plugin: go  # $PATH に protoc-gen-go がある場合
    out: gen/go
    opt: paths=source_relative
  - plugin: buf.build/grpc/go  # buf のリモートプラグイン(最新)を使用
#  - plugin: buf.build/grpc/go:v1.2.0  # リモートのプラグインを使用
#  - plugin: go-grpc  # $PATH に protoc-gen-go-grpc がある場合
    out: gen/go
    opt:
      - paths=source_relative
      # - require_unimplemented_servers=false
```

※ connect-goの場合
```yaml
version: v1
plugins:
    - plugin: buf.build/protocolbuffers/go
      out: gen/go
      opt: paths=source_relative
    - plugin: buf.build/bufbuild/connect-go
      out: gen/go
      opt: paths=source_relative
```

コマンド
```sh
buf generate  # 
buf generate <フォルダ>  # .proto が全てサブフォルダにある場合
```

- リモート（BSR:  [Buf Schema Registry](https://buf.build/docs/bsr/introduction/)）から生成
```bash
buf generate buf.build/acme/petapis
```

- 使用ファイルの指定
```bash
buf generate --template buf.gen.go.yaml
buf generate --template buf.gen.java.yaml
```

- 出力先の指定
```bash
buf generate https://github.com/foo/bar.git --template data/generate.yaml -o bar
```

`--path` でのファイル指定や、docker 実行等も可能
ref : https://buf.build/docs/generate/usage/#5.4.-limit-to-specific-files


### ローカルでコード生成せず、BSR のコードを利用

BSR への登録
ref: https://buf.build/docs/bsr/module/dependency-management/#add-a-dependency

buf.gen.yaml は削除
```diff
 package main

 import (
     "context"
     "fmt"
     "log"

-    // This import path is based on the name declaration in the go.mod,
-    // and the gen/proto/go output location in the buf.gen.yaml.
-    petv1 "github.com/bufbuild/buf-tour/petstore/gen/proto/go/pet/v1"
+    petv1 "buf.build/gen/go/<USERNAME>/petapis/protocolbuffers/go/pet/v1"
+    "buf.build/gen/go/<USERNAME>/petapis/grpc/go/pet/v1/petv1grpc"
     "google.golang.org/grpc"
 )
```

### ビルド

jsonやバイナリ(bin), zip, tar 等出力できる

オプションなしは何も出力しない
```bash
buf build
```

オプションなしは以下と同じ
```bash
protoc \
    -I proto \
    -I vendor/protoc-gen-validate \
    -o /dev/null \
    $(find proto -name '*.proto')
```

buf buildの場合のフォルダ構成とbuf.work.yaml 
```
.
├── buf.work.yaml
├── proto
│   ├── acme
│   │   └── weather
│   │       └── v1
│   │           └── weather.proto
│   └── buf.yaml
└── vendor
    └── protoc-gen-validate
        ├── buf.yaml
        └── validate
            └── validate.proto
```

```yaml
# buf.work.yaml
version: v1
directories:
  - proto
  - vendor/protoc-gen-validate
```

パッケージ一覧表示
```bash
buf build -o -#format=json | jq '.file[] | .package' | sort | uniq | head
```

```bash
$ buf build -o image.bin
$ buf build -o image.json
$ buf build -o image.zip
$ buf build -o image.tar
```
※ 指定可能フォーマット：https://buf.build/docs/reference/inputs/format/#options

出力対象を絞ったり、docker実行等もできる

ref: https://buf.build/docs/build/usage/

### buf curl による API実行

Connect、gRPC、またはgRPC-WebサーバでRPCを呼び出せる

```bash
buf curl \
    --data '{"sentence": "I feel happy."}' \
    https://demo.connect.build/buf.connect.demo.eliza.v1.ElizaService/Say
```

デフォルトの RPC プロトコルは Connect。変更可能
```bash
 buf curl --protocol grpc --http2-prior-knowledge \
    http://localhost:20202/foo.bar.v1.FooService/DoSomething
```
※ http を使用する場合、HTTP/1.1 が使用されるため、HTTP/2を使うためには --http 2-prior-knowledge が必要

buf curl の実行は、サーバがサーバリフレクションサービスを公開することを期待する。ない場合は、`--schema` の指定が必要。値は BSR でも、Gitリポジトリでも、ローカルでも可

```bash
buf curl \
   --schema buf.build/bufbuild/eliza \
   --data '{"name": "Bob Loblaw"}' \
   https://demo.connect.build/buf.connect.demo.eliza.v1.ElizaService/Introduce
```

サーバリフレクションとは
- スタブ情報をクライアントにプリコンパイルすることなく、リクエストの実行時構築でクライアントを支援するサーバーのオプションの拡張機能
- サーバー リフレクションの主な使用例は、(通常は) grpc サーバーと通信するためのコマンドラインデバッグツールを作成すること
	- ペイロード（人が読める）形式と、サーバが扱うバイナリ形式の変換を担う
https://github.com/grpc/grpc/blob/master/doc/server-reflection.md

ref: https://buf.build/docs/curl/usage/

### BSR への 登録/更新

前提： https://buf.build/settings/user にアクセスして トークンを作成

- ログイン
```
buf registry login
```

- 画面からリポジトリ作成
- buf.yaml があるフォルダに移動
- buf.yaml に `name: <リポジトリ名>` を追加
- buf push　（更新する度実行）

タグ付けも可能
```sh
buf push --tag "$(git rev-parse HEAD)"
```

依存関係がある場合は、以下で更新可能
```sh
buf mod update
```

ref:
- https://buf.build/docs/tutorials/getting-started-with-bsr/
- https://buf.build/docs/how-to/iterate-on-modules/

#buf
#protoc
#gRPC