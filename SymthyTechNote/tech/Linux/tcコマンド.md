
ネットワーク遅延
```shell
sudo tc qdisc add dev eth0 root netem delay 100m
```

パケットロス
```shell
sudo tc qdisc add dev eth0 root netem loss 30%
```

設定確認
```shell
sudo tc qdisc show dev eth0
```

設定解除
```shell
sudo tc qdisc del dev eth0 root
```

[tc コマンドでネットワーク遅延やパケットロスを疑似的に発生させるメモ](https://inokara.hateblo.jp/entry/2016/02/14/191853)