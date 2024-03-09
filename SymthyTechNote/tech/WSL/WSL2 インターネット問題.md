
[VPN を有効にすると WSL2 でインターネットに出られない問題に対処する](https://zenn.dev/mallowlabs/articles/vpn-on-wsl2-ubuntu)


wsl の IP 確認
```
ip addr show eth0 | grep "inet\b" | awk '{print $2}' | cut -d/ -f1
```