
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


refs: [Googleが開発する最新ビルドツール「Bazel」を使ってみよう](https://knowledge.sakura.ad.jp/6174/)

## 特徴

「WORKSPACE」と「BUILD」という2つのファイルを使ってビルドルールを定義

メリット
- さまざまな言語/目的に対応
	- AndroidやC/C++、Java、Objective-Cといった言語向けのルールがあらかじめ用意されている
	- C/C++やJava、Python、シェルスクリプトなどについてはテストを実行するためのルールも用意されている
- ビルドによってディレクトリを汚染しない
	- ビルドやテストはデフォルトではサンドボックス化された環境で行われるため、ビルドやテストがそれを実行しているシステムに影響を及ぼす可能性が最小限に抑えられている
- 並列ビルド

デメリット
- 言語毎にビルドルールを用意することで記述量を大幅に削減しているが、その副作用として実際にどのような処理が実行されるのかが分かりにくくなっている。そのため、トラブルが発生した際にその原因を突き止めることが難しくなる可能性がある

## Sample

- https://github.com/bazelbuild/bazel/tree/master/examples
- https://github.com/bazelbuild/examples