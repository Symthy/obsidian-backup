
## 手動で repository 設定

/etc/yum.repos.d にファイル作成

rhel-dvd.repo
```
[dvd-appstream]
name=appstream
baseurl=file:///mnt/REHL_SV_8.4_x64/AppStream/
enabled=1
gpgcheck=0

[dvd-baseos]
name=baseos
baseurl=file:///mnt/RHEL_SV_8.4_x64/BaseOS/
enabled=1
gpgcheck=0
```