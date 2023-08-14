
- 依存パッケージ確認
```sh
rpm -qpR <rpmパス>
```

- スクリプト確認
```shell
rpm -qp --scripts <rpmパス>
```

- rpm インストール有無
```sh
rpm -q <パッケージ名> > /dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "non exit"
fi
```

#shell/rpm
