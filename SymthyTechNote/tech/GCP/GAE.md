
## 構築


- [公式ドキュメント - App Engine で Go アプリをビルドする](https://cloud.google.com/appengine/docs/standard/go/building-app?hl=ja#local-machine)
- [Google App Engine（GAE）とは 概要や機能、活用事例を5分で入門](https://cloud-ace.jp/column/detail251/)


アプリ開発の基本的な流れ（Golang）
https://cloud.google.com/appengine/docs/standard/go/building-app?hl=ja

プロジェクトセレクタ
- アプリ作成
![[Pasted image 20230617190029.png]]

Googole Cloud SDK 入手して、自PCで設定要
![[Pasted image 20230617190245.png]]

先にgcloud CLI インストール
https://cloud.google.com/sdk/docs/install?hl=ja

Googole Cloud SDK のインストールは以下参照
https://cloud.google.com/sdk/docs/install-sdk?hl=ja

※Windowsの場合
- インストーラをダウンロードするか
- PowerShell でコマンド実行するか

PowerShellの場合。インストール完了して、run gclound init にチェック付けたら色々聞かれた

```
Welcome to the Google Cloud CLI! Run "gcloud -h" to get the list of available commands.
---
Welcome! This command will take you through the configuration of gcloud.

Your current configuration has been set to: [default]

You can skip diagnostics next time by using the following flag:
  gcloud init --skip-diagnostics

Network diagnostic detects and fixes local network connection issues.
Checking network connection...done.
Reachability Check passed.
Network diagnostic passed (1/1 checks passed).

You must log in to continue. Would you like to log in (Y/n)?  Y

Your browser has been opened to visit:

  <省略>

Pick cloud project to use:
 [1] dependable-aloe-252213
 [2] extended-poetry-390109
 [3] intense-guru-390109
 [4] quickstart-1567907311069
 [5] sym-coding-exam-api-server
 [6] Enter a project ID
 [7] Create a new project
Please enter numeric choice or text value (must exactly match list item):5

Your current project has been set to: [sym-coding-exam-api-server].

Not setting default zone/region (this feature makes it easier to use
[gcloud compute] by setting an appropriate default value for the
--zone and --region flag).
See https://cloud.google.com/compute/docs/gcloud-compute section on how to set
default compute region and zone manually. If you would like [gcloud init] to be
able to do this for you the next time you run it, make sure the
Compute Engine API is enabled for your project on the
https://console.developers.google.com/apis page.

Created a default .boto configuration file at [C:\Users\sympa\.boto]. See this file and
[https://cloud.google.com/storage/docs/gsutil/commands/config] for more
information about configuring Google Cloud Storage.
Your Google Cloud SDK is configured and ready to use!

* Commands that require authentication will use sympathia3104@gmail.com by default
* Commands will reference project `sym-coding-exam-api-server` by default
Run `gcloud help config` to learn how to change individual settings

This gcloud configuration is called [default]. You can create additional configurations if you work with multiple accounts and/or projects.
Run `gcloud topic configurations` to learn more.

Some things to try next:

* Run `gcloud --help` to see the Cloud Platform services you can interact with. And run `gcloud help COMMAND` to get help on any gcloud command.
* Run `gcloud topic --help` to learn about advanced features of the SDK like arg files and output formatting
* Run `gcloud cheat-sheet` to see a roster of go-to `gcloud` commands.

C:\Users\sympa\AppData\Local\Google\Cloud SDK>
```

デプロイ： gcloud app deploy
ブラウザ起動：gcloud app browse

### クイックスタート(Go +1.12)

サンプルデプロイ
https://cloud.google.com/appengine/docs/standard/go/create-app?hl=ja

Go 1.12+ 用の App Engine 拡張機能を含む [gcloud コンポーネント](https://cloud.google.com/sdk/docs/managing-components?hl=ja)をインストール
gcloud components install app-engine-go
![[Pasted image 20230618151554.png]]

gcloud app deploy 実行
```sh
D:\dev_git\work\golang-samples\appengine\go11x\helloworld>gcloud app deploy
You are creating an app for project [intense-guru-390109].
WARNING: Creating an App Engine application for a project is irreversible and the region
cannot be changed. More information about regions is at
<https://cloud.google.com/appengine/docs/locations>.

Please choose the region where you want your App Engine application located:
＜リージョン選択省略＞

Creating App Engine application in project [intense-guru-390109] and region [asia-northeast1]....done.
Services to deploy:

descriptor:                  [D:\dev_git\work\golang-samples\appengine\go11x\helloworld\app.yaml]
source:                      [D:\dev_git\work\golang-samples\appengine\go11x\helloworld]
target project:              [intense-guru-390109]
target service:              [default]
target version:              [20230618t152323]
target url:                  [https://intense-guru-390109.an.r.appspot.com]
target service account:      [intense-guru-390109@appspot.gserviceaccount.com]


Do you want to continue (Y/n)?  Y

Beginning deployment of service [default]...
Created .gcloudignore file. See `gcloud topic gcloudignore` for details.
╔════════════════════════════════════════════════════════════╗
╠═ Uploading 6 files to Google Cloud Storage                ═╣
╚════════════════════════════════════════════════════════════╝
File upload done.
Updating service [default]...done.
Setting traffic split for service [default]...done.
Deployed service [default] to [https://intense-guru-390109.an.r.appspot.com]

You can stream logs from the command line by running:
  $ gcloud app logs tail -s default

To view your application in the web browser run:
  $ gcloud app browse
```

ローカルでのログ監視
```
gcloud app logs tail -s default
```




## app.yaml 設定

- 設定項目まとめ： [App Engine Scaling Config](https://qiita.com/sinmetal/items/017e7aa395ff459fca7c)

名前を指定可能。名前を省略すると、`default` として扱われる

## 実装

urlfetchは古い。v2で使わずとも外部APIと通信できるようになっているため不要
-  [GAE/Go の urlfetch のタイムアウトを設定する](https://www.pospome.work/entry/2017/12/17/112144)
- [GAE/Go urlfetch.ClientでGetしたらタイムアウトエラー「urlfetch: DEADLINE_EXCEEDED」の解決方法](https://qiita.com/hirocueki2/items/56065f3ab46bf455253e)

### GCS

公式ドキュメント： https://cloud.google.com/appengine/docs/standard/using-cloud-storage?hl=ja&tab=go#go

GCS から取得
- [GCSから取得する時](https://ema-hiro.hatenablog.com/entry/2018/04/20/011427)
- [GCSに保存してあるデータを取得する](https://ema-hiro.hatenablog.com/entry/2018/04/20/011427)

```go
func main() {
    ctx := context.Background()
    clinet, err := storage.NewClient(ctx)
    if err != nil {
        log.Fatal(err)
    }
	reader, err := clinet.Bucket(constants.GCS_BUCKET_NAME).Object("ファイル名").NewReader(ctx)  // 戻り値: io.Reader
    if err != nil {
        log.Fatal(err)
    }
    // :
}
```



GCSへアップロード
https://cloud.google.com/storage/docs/streaming-uploads?hl=ja#storage-stream-upload-object-go

GCSのオブジェクト削除
https://cloud.google.com/storage/docs/deleting-objects?hl=ja

### ログ出力

- https://cloud.google.com/logging/docs/setup/go?hl=ja
- https://cloud.google.com/appengine/docs/standard/go/writing-application-logs?hl=ja
- [GAE で Stackdriver Logging にログを拾わせてトレースしたい](https://scrapbox.io/pokutuna/GAE_%E3%81%A7_Stackdriver_Logging_%E3%81%AB%E3%83%AD%E3%82%B0%E3%82%92%E6%8B%BE%E3%82%8F%E3%81%9B%E3%81%A6%E3%83%88%E3%83%AC%E3%83%BC%E3%82%B9%E3%81%97%E3%81%9F%E3%81%84)
- [GCPで理想の構造化ログを出力する方法](https://zenn.dev/glassonion1/articles/c58505bf594868)

### その他参考になりそうなもの

- [gcloudでプロジェクトを切り替える](https://zenn.dev/taka_baya/articles/ef12fe9a043560)
- [Go+chi+GAEでクリーンアーキテクチャを試してみた](https://rinoguchi.net/2022/08/go-crean-achitecure.html)
- [超簡単 Google App Engineで始めるWebアプリケーション 〜リクエスト分割機能がすごかった〜](https://dev.classmethod.jp/articles/gae-webapp/#toc-1)  2019
- [Google App Engine (GAE) チュートリアル – GAE 機能確認編](https://cloud-textbook.com/2804/#i-7)  2019
- [【GCP】AppEngineにGolangアプリをデプロイしちゃうぞ【DockerとCloudSQLもあるよ】](https://zenn.dev/chillout2san/articles/c7b1bd4feb8800) 2022