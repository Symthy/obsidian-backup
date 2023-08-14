https://mondai.ping-t.com/question_subjects/72/question_sessions/15162138/progressing_questions/232944239/edit?no_selection=false

# Auto Scalling

- AWS Auto Scaling
	- AWSリソースを負荷状況や設定したスケジュールに従って、自動的にスケーリングする機能
- スケーリング
	- スケールアウト（リソースの増加）またはスケールイン（リソースの削減）を実施すること

## Auto Scalingグループ

- 自動スケーリング対象の管理単位
- Auto Scalingグループ内でのリソースの最小数および最大数や、スケールアウト/インの実行条件などを指定

![[Pasted image 20230225145059.png]]

## ライフサイクルフック

[【新機能】Auto Scalingのインスタンス起動/破棄時に初期処理/終了処理を追加 – LifeCycleHook機能のご紹介](https://dev.classmethod.jp/articles/autoscaling-lifecyclehook/)

スケーリング発生によるEC2インスタンスの起動または終了時に任意の処理を実行する機能
- スケールアウトによって新たに追加したインスタンスに初期化スクリプトを実行
- スケールインによって終了するインスタンスにログを収集・退避させるといった処理実行

## ヘルスチェック設定

ヘルスチェックのタイプには「EC2」と「ELB」がある

「EC2」が有効、「ELB」が無効
  → ELBからのヘルスチェックに応答がないインスタンスは負荷分散先からは除外されますが、EC2の「ステータスチェック」が正常であればインスタンスが終了されない
  → **最小キャパシティ未満のインスタンスで負荷分散することがある**


#AWS/AutoScaling 