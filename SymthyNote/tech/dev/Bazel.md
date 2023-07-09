refs: 
- 公式ドキュメント：https://bazel.build/about?hl=ja
- [Googleが開発する最新ビルドツール「Bazel」を使ってみよう](https://knowledge.sakura.ad.jp/6174/)
-  [Bazelを使ってみる その１（Goのビルド）](https://christina04.hatenablog.com/entry/using-bazel-to-build-go)

## 手順

bazel のバージョン管理が Bazelisk でできる

[Bazelisk を使用した Bazel のインストール / 更新](https://bazel.build/install/bazelisk?hl=ja)
windowsの場合
- chocolatey でインストール
- バイナリをGithubから取得して、改名して配置、Pathにはフォルダパスを指定
	- ref: [Windows環境にbazelを導入してみた](https://qiita.com/ma-szk/items/05bfc4d0c731071fdeec)
![[Pasted image 20230701234622.png]]

```
$ bazelisk
2023/07/01 23:25:09 Downloading https://releases.bazel.build/6.2.1/release/bazel-6.2.1-windows-x86_64.exe...
WARNING: Invoking Bazel in batch mode since it is not invoked from within a workspace (below a directory having a WORKSPACE file).
 :
```



## 特徴

- 「WORKSPACE」と「BUILD」という2つのファイルを使ってビルドルールを定義
- 優れたビルドツールである一方で、導入したチームには１人はBazel職人が必要と言われるほどキャッチアップコストが高くハマったときに開発が止まると言われている


メリット
- さまざまな言語/目的に対応
	- AndroidやC/C++、Java、Objective-Cといった言語向けのルールがあらかじめ用意されている
	- C/C++やJava、Python、シェルスクリプトなどについてはテストを実行するためのルールも用意されている
- ビルドによってディレクトリを汚染しない
	- サンドボックス環境で実行される（Bazel１つだけあればビルドできる）
	- ビルドやテストはデフォルトではサンドボックス化された環境で行われるため、ビルドやテストがそれを実行しているシステムに影響を及ぼす可能性が最小限に抑えられている
- 並列ビルド
- ビルドの再現性が保たれる
- キャッシュの活用（ローカル・リモート）によるビルド時間の高速化


デメリット
- 言語毎にビルドルールを用意することで記述量を大幅に削減しているが、その副作用として実際にどのような処理が実行されるのかが分かりにくくなっている。そのため、トラブルが発生した際にその原因を突き止めることが難しくなる可能性がある

「inputが同じであればoutputも同じ」という再現性のため、[Bazelは**inputの明示を重視**](https://docs.bazel.build/versions/4.2.2/build-ref.html#dependencies)。
input:
- ソースコード
- 依存ライブラリ（内部・外部）
- 環境変数
- コンパイル環境（言語、ツール）
- 実行ホストの環境
等
※大抵Bazelのビルドでコケるのはinput（特に依存ライブラリの関係）が不足しているケースが多い


## Sample

- https://github.com/bazelbuild/bazel/tree/master/examples
- https://github.com/bazelbuild/examples

## Goでの利用



ref:  [Bazelを使ってみる その１（Goのビルド）](https://christina04.hatenablog.com/entry/using-bazel-to-build-go)

