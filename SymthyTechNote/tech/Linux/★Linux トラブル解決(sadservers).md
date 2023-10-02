
https://sadservers.com/

## １．ログファイル出力プロセス特定＆kill

```
$ fuser -v /var/log/bad.log
                     USER        PID ACCESS COMMAND
/var/log/bad.log:    ubuntu      641 F.... badlog.py

$ ps aux | head -1 && ps aux | grep badlog.py
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
ubuntu       641  0.0  1.7  17672  8000 ?        S    03:00   0:00 /usr/bin/python3 /home/ubuntu/badlog.py
ubuntu      1179  0.0  0.4   7008  2060 pts/0    R+   03:04   0:00 grep --color=auto badlog.py
kill -9 641
```

## ２．アクセス最多IP特定

```
$ cat /home/admin/access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head
    482 66.249.73.135
    364 46.105.14.53
    357 130.237.218.86
    273 75.97.9.59
    113 50.16.19.13
    102 209.85.238.199
     99 68.180.224.225
     84 100.43.83.137
     83 208.115.111.72
     82 198.46.149.143
```

## ３．