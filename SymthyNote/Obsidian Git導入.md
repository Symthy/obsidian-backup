
ref [Obsidian Gitを導入してみた](https://zenn.dev/ayumukob/articles/3b034fcb6874d2)


# はじめに

最近話題のObsidianを触り始めたということで、mdのバックアップ先をGitHubに決め、コミュニティプラグインであるObsidian Gitを使用して連携しはじめました。  
日本語での各種設定内容の説明がなかったので、現時点(2022/12/22)での設定項目等をまとめていきたいと思います。

# [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#obsidian-git%E6%A6%82%E8%A6%81)Obsidian Git概要

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#%E3%83%A1%E3%82%A4%E3%83%B3%E3%83%95%E3%82%A3%E3%83%BC%E3%83%81%E3%83%A3%E3%83%BC)メインフィーチャー

-   指定の時間(分)ごとに自動的にバックアップしてくれる
-   Obsidian起動時にリポジトリからpullしてくれる
-   pull/pushにホットキーを割り当てられる
-   [サブモジュールに対応](https://github.com/denolehov/obsidian-git/wiki/#submodules-support)
-   `Source Control View`で個々のファイルのgit操作ができる

# [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB%E6%96%B9%E6%B3%95)インストール方法

Obsidianのコミュニティプラグインから導入可能です。  
`"設定" -> "コミュニティプラグイン" -> "コミュニティプラグイン 閲覧を押下", "Obsidian Git"で検索, インストール, 有効化.`  
※一応マニュアルでもインストールできるがここでは割愛

# [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#%E9%80%A3%E6%90%BA%E6%96%B9%E6%B3%95)連携方法

1.  GitHubにバックアップ用のリポジトリを作成しておきます。
2.  VaultとなるディレクトリにGitを構成します。  
    やり方はいくつかあると思いますが、一番楽だと思うのはgit cloneして上記で作成したリポジトリをローカルに落とし、そのディレクトリをObsidianでVaultとして読み込むやり方だと思います。
3.  `Source Control View`のステータスバーに`Git is ready`と表示されていたら成功です。

# [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#.gitignore).gitignore

コマンドパレットで`Edit .gitignore`を実行し、以下を記述します。

```
# to exclude Obsidian's settings (including plugin and hotkey configurations)
.obsidian/

# OR only to exclude workspace cache
.obsidian/workspace.json

# Add below lines to exclude OS settings and caches
.trash/
.DS_Store
```

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89)コマンド

-   List changed files
-   Open diff view
-   Stage current file
-   Unstage current file
-   Commit all changes  
    pushはしない。
-   Commit all changes with specific message  
    カスタムメッセージを指定できる`Commit all changes`。
-   Commit staged  
    ステージングされたファイルのみcommit。
-   Commit staged with specific message  
    カスタムメッセージを指定できる`Commit staged`。
-   Create Backup  
    `Push on backup`設定がオンの場合はpushも実行。
-   Create Backup with specific message  
    カスタムメッセージを指定できる`Create Backup`。
-   Create backup and close  
    処理完了時にデスクトップ版を終了してくれる`Create Backup`。モバイル版の場合は終了できない。
-   Push
-   Pull
-   Edit remotes
-   Remove remote
-   Clone an existing remote repo  
    モバイルで有効なコマンド？
-   Open file on GitHub  
    デフォルトのブラウザでGitHub上の現在のファイルの開く。デスクトップ版だけの機能。
-   Open file history on GitHub  
    デフォルトのブラウザでGitHub上の現在のファイルの履歴を開く。デスクトップ版だけの機能。
-   Initialize a new repo
-   Create new branch
-   Delete branch
-   CAUTION: Delete repository
-   Open source control view  
    `Source Control View`をサイドペインに表示。
-   Edit .gitignore
-   Add file to .gitignore  
    現在のファイルを.gitignoreに追加。

# [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#%E5%90%84%E7%A8%AE%E8%A8%AD%E5%AE%9A)各種設定

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#automatic)Automatic

自動化設定

-   Split automatic commit and push  
    commit/pushを別々のタイマー設定で行うか。
-   Vault backup interval(minutes)  
    Vaultのバックアップ(commit/push)間隔(分)の設定。0（デフォルト）を指定すると無効。
-   Auto Backup after file change  
    ファイル変更後にバックアップするか。オンにした場合、最後の変更後0分ごとに自動バックアップを行う。
-   Auto backup after lastest commit  
    最後のコミット後にバックアップするか。`Auto Backup after file change`とどちらかが設定できる。
-   Auto pull interval(minutes)  
    自動的にpullをする間隔(分)。0（デフォルト）を指定すると無効。
-   Specify custom commit message on auto backup  
    バックアップ時にコミットメッセージを入力するためのポップアップを表示するかどうか。
-   Commit message on auto buckup/commit  
    自動コミットメッセージの指定。`{{date}}`、`{{hostname}}`、`{{numFiles}}`は利用可能。デフォルトは`vault backup: {{date}}`。

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#commit-message)Commit Message

コミットメッセージ設定

-   Commit message on manual backup/commit  
    手動コミットメッセージの指定。`{{date}}`、`{{hostname}}`、`{{numFiles}}`は利用可能。デフォルトは`vault backup: {{date}}`。
-   {{date}} placeholder format  
    日付のフォーマット。デフォルトは`YYYY-MM-DD HH:mm:ss`
-   {{hostname}} placeholder replacement  
    ホスト名の置き換え。カスタムしたホスト名を指定できる。
-   preview commit message  
    コミットメッセージのプレビュー。設定というより今の状態を確認できる。
-   List filenames affected by commit in the commit body  
    コミット本体でcommitの影響を受けるファイル名を一覧表示する。※確認不十分。

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#buckup)Buckup

バックアップ設定

-   Sync Method  
    リモートとの差分があった時の処理方法。以下が選択可能。デフォルトは`Merge`。
    -   Merge
    -   Rebase
    -   Other sync service (Only updates the HEAD without touching the working directory)
-   Pull updates on startup  
    Obsidian起動時に自動的にpullするか。
-   Push on backup  
    バックアップ時にpushするか。commitだけしたい場合はオフにする。
-   Pull changes before push  
    pushする前にpullするか。pushが有効な場合のみの設定。

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#miscellaneous)Miscellaneous

その他の設定

-   Automatically refresh Source Control View on file changes  
    ファイル変更時にSource Control Viewを自動更新するかどうか。マシンスペックによってはラグる可能性があるので、その場合はオフ。
-   Source Control View refresh interval  
    Source Control Viewの更新間隔(ミリ秒)。デフォルトは7000
-   Disable notifications  
    git操作に関する通知を無効化。オンにしてもエラーに関するものは通知される。
-   Show status bar  
    ステータスバーを表示するか。
-   Show branch status bar  
    ステータスバーにブランチを表示するか。
-   Show changes files count in status bar  
    ステータスバーに変更ファイル数を表示するか。

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#commit-author)Commit Author

Author設定

-   Author name for commit  
    Authorの名前
-   Author email for commit  
    Authorのメールアドレス

## [](https://zenn.dev/ayumukob/articles/3b034fcb6874d2#advanced)Advanced

高度な設定

-   Update submodules  
    サブモジュールの更新機能をオンにするか。
-   Custom git binary path  
    gitの実行ファイルのパスを指定。
-   Additional PATH environment variable paths  
    環境変数`PATH`に設定を追加。
-   Reload with new PATH environment variable  
    `Additional PATH environment variable paths`で設定したものを反映させる。
-   Custom base path(Git repository path)  
    gitを実行するためのvaultへの相対パスを設定。ディレクトリ構成を柔軟にしたいときに使う感じか？
-   Disable on this device  
    プラグイン自体の無効化。
-   Donate  
    寄付。