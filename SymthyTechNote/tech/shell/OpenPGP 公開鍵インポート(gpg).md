
OpenPGP 電子メールなどのメッセージの暗号化やデジタル署名のために広く利用されている公開鍵暗号の規格の総称
https://mitome.in/openpgp/

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
gpg コマンドで --dearmor つまりバイナリに変換して格納している。 apt-key add コマンドで鍵束ファイルに追加するのではなく，公開鍵データをそのまま単独ファイルとして置いているのがポイント


これまで（api-keyは非推奨)
code:sh
 # 公開鍵登録
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

公開鍵登録したらあとは同じ
```sh
# リポジトリ登録
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
# インストール
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
  
ref: https://zenn.dev/spiegel/articles/20220508-apt-key-is-deprecated#サードパーティの-openpgp-公開鍵のインポート

#shell 
#gpg