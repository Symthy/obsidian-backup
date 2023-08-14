
ref: [これから始める継続的インテグレーション- Github Actions](https://speakerdeck.com/devops_vtj/korekarashi-meruji-sok-de-integuresiyon-github-actionsbian)

## ランナー

- Githubホステッドランナー
	- 毎回クリーンな状態で起動
	- 何も意識せずに使用開始可能
- セルフホステッドランナー
	- 自分でメンテ
	- 前回終了時の状態で起動（ゴミが残っていたりする）
	- パッケージも自分で用意
	- 節約のために使おうとすると逆に高コスト
		- Githubホステッドランナーでは満たせない要件の時に使用を検討
		- ※別のCIに乗り換えることも視野に入れる

## CircleCIとの違い

- CircleCIの無料利用枠は時間ではなく、クレジット
	- マシンタイプやストレージの使用に応じてクレジット消費
- Freeプランで使う分にはそう変わらない
- 名称が違う
	- Github Actionsのアクション = CircleCI の orbs
	- Github Actionsのランナー = CircleCI の Executor

## Github Actions > Circle CI

- あらゆるイベントをトリガーできる
- 特定のファイルやディレクトリの変更をトリガーにできる
	- CircleCIにはない機能
		- orbs を使えば実現できる

### Github Actions を使う時

- Github を使っている
- パブリックリポジトリがメイン
- CIに高機能を求めていない
	- UIやダッシュボードなど
- 簡単にはじめたい

### Circle CI を使う時

- GitLab や Bitbucket を使っている
- プライベートリポジトリが多い
- ワークフローを早く終わらせたい
- 高機能なCIを使いたい

## Github Actions のデバッグ

- Circle CI のようにランナーにSSHすることはできない
- Github Actions にはロギング機能がある
- 診断ロギング
	- ジョブの実行に関するログファイルを出力
	- シークレットの名前に「ACTIONS_RUN_DEBUG」。値に「true」を設定で有効化
- デバッグロギング
	- ジョブの実行ログが詳細になる
	- シークレットの名前に「ACTIONS_STEP_DEBUG」。値に「true」を設定で有効化

### ACTIONS_RUN_DEBUG の設定
gh コマンドで設定
```sh
gh sercret set ACTIONS_RUNNER_DEBUG
```

GUIから設定
![[Pasted image 20230525220537.png]]


### 診断ロギング取得

ghコマンドでログ出力
```sh
gh run list
```

GUIからダウンロード
![[Pasted image 20230525220754.png]]

### デバッグロギング

![[Pasted image 20230525221122.png]]

画面上でも有効化できる模様（一時的に？）
![[Pasted image 20230525221234.png]]

## ツール

### gh コマンド
- Github公式のCLIツール
- ワークフローやジョブの状態を見れる
- issue作成可能
- pull request にコメント 等可能

![[Pasted image 20230525221432.png]]

### actionlint

ワークフロー(yaml) の静的解析ツール

(Web) actionlint playground

### act

ワークフローをローカルで実行

### まとめ

- Circle CI は高機能。チョット使うだけだとオーバースペック
	- 割と単純なことをするなら、Github Actions で十分
- 毎日の issue の確認や整理も Github Actions で自動化できる