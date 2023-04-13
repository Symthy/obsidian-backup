

## rpm 作成 (fpm)

 [fpm を使って手軽に rpm パッケージを作ろう](https://tech.aptpod.co.jp/entry/2020/11/11/090000)

```shell
fpm \
  :
  --before-install ./rpm_helper/pre.sh \
  --after-install ./rpm_helper/post.sh \
  --before-remove ./rpm_helper/preun.sh \
  --after-remove ./rpm_helper/postun.sh \
```


## スクリプト上での引数

引数$1に実行時の値が入ってくるのでそれを使って判定

実行順序は、pre→post→preun→postun
※preun と postun は 古い媒体のスクリプトが実行される

[Fedora Packaging Guidelines for RPM Scriptlets - Fedora Project Wiki](https://fedoraproject.org/wiki/Packaging:Scriptlets)  

- [rpm作成時%preunと%postunには気をつけよう](https://qiita.com/ymko/items/80e78bfc8cbf4cee2186#%E3%81%A9%E3%81%86%E3%81%99%E3%82%8C%E3%81%B0%E3%81%84%E3%81%84%E3%81%AE%E3%81%8B)
- [pre,post,preun,postunの$1の値](https://qiita.com/todanano/items/7ee8bb6f496000b48bae)

#shell/rpm 