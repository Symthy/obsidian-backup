
- 隠しファイルも含めてコピー

```sh
cp -r "test/." [対象のフォルダ]

# 以下では隠しファイルはコピーされない
# cp -r test/* [対象のフォルダ]
```

ref: [cpコマンドで不可視ファイル（ドットファイル）までコピーする](https://ex1.m-yabe.com/archives/3913)