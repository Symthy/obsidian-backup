
## 構築

参考： [Google App Engine（GAE）とは 概要や機能、活用事例を5分で入門](https://cloud-ace.jp/column/detail251/)

作成
![[Pasted image 20230617190029.png]]

Googole Cloud SDK 入手して、自PCで設定？実施
![[Pasted image 20230617190245.png]]

Google CLOUD SDK インストール
https://cloud.google.com/sdk/docs/install-sdk?hl=ja
Windowsの場合
- インストーラをダウンロードするか
- PowerShell でコマンド実行するか

インストール完了して、run gclound init にチェック付けたら色々聞かれた

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

    https://accounts.google.com/o/oauth2/auth?response_type=code&client_id=32555940559.apps.googleusercontent.com&redirect_uri=http%3A%2F%2Flocalhost%3A8085%2F&scope=openid+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fuserinfo.email+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fappengine.admin+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fsqlservice.login+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcompute+https%3A%2F%2Fwww.googleapis.com%2Fauth%2Faccounts.reauth&state=psjQJqnLIC7YV4CdhkvzZ4ti6iHMiQ&access_type=offline&code_challenge=SDc-KKA0OVrOgyuBAqeSy1zXKnQqfNjlqQ9_ankus8A&code_challenge_method=S256

You are logged in as: [sympathia3104@gmail.com].

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

### クイックスタート(Go +1.12)

サンプルデプロイ
https://cloud.google.com/appengine/docs/standard/go/create-app?hl=ja


インストール
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

アプリ開発の基本的な流れ
https://cloud.google.com/appengine/docs/standard/go/building-app?hl=ja



## ログ

https://cloud.google.com/logging/docs/setup/go?hl=ja


## app.yaml 設定

- [App Engine Scaling Config](https://qiita.com/sinmetal/items/017e7aa395ff459fca7c)

## 実装

urlfetchは古い。使わずとも外部APIと通信できる
-  [GAE/Go の urlfetch のタイムアウトを設定する](https://www.pospome.work/entry/2017/12/17/112144)

GCS から取得
- [GCSから取得する時](https://ema-hiro.hatenablog.com/entry/2018/04/20/011427)

GCSへアップロード
https://cloud.google.com/storage/docs/streaming-uploads?hl=ja#storage-stream-upload-object-go