

## 複数ファイル文字列一括置換
```sh
find ./ -type f -name \*.json -exec sed -i -e 's#before#after#g' {} \;
```

- type f  ：ファイルのみ対象

[ファイルなどを検索する！findコマンドの詳細まとめ【Linuxコマンド集】](https://eng-entrance.com/linux-command-find#-exec)

[# 複数のテキストファイル内の文字列をコマンドで一括置換する方法](https://linuxfan.info/post-2034)

## ディレクトリ下のファイル一覧

```sh
find "${dir_path}" -type f
# 特定のファイルのパス取得
find "${dir_path}" -type f | grep "${file_name}"
```

## ファイル内検索

[[shell ファイル内検索]]

#shell/find 