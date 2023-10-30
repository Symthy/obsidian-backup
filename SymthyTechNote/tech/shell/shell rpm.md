
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

RPM単位の依存パッケージ抽出
```sh
for r in $(find <dir_path> -name "*.rpm*" | grep -v el7 | grep -v rhel7); do (echo; echo $r; rpm -qp --requires $r 2>/dev/null | cut -d ' ' -f 1 | xargs rpm -q --whatproviders 2>/dev/null | sort | uniq | grep "rpmlib("); done
```

#shell/rpm
