
# 型 
## list / tuple

- list は ミュータブル
- tuple はイミュータブル
	- どうしても更新したい場合は list に変換して操作

ref: 
- [note.nkmk.me：Pythonでリストとタプルを相互に変換するlist(), tuple()](https://note.nkmk.me/python-list-tuple-convert/)
- [note.nkmk.me：Pythonでタプルの要素を追加・変更・削除](https://note.nkmk.me/python-tuple-operation/)

## dict 

辞書のkey は イミュータブル
- list や dict を key にできない
- tuple は イミュータブルなため、key にできる

ref: [辞書のキーに使えるのはリスト？タプル？- Python独学記](https://blog.pyq.jp/entry/python_story_190701)

### defaultdict

dict の初期化を関数にしたがって実施

`d = collections.defaultdict(list)` は `d={:[]}` となる（デフォルトのvalue値にリストオブジェクト（型）が入ったdict型の変数d）

```python
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d = defaultdict(list)
for k, v in s:
    d[k].append(v)
list(d.items())

[('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```

ref: [defaultdict()についてわかりやすくまとめて見た](https://kyam-013.hatenablog.jp/entry/2017/07/21/021106)

# 関数

## ord()

Unicodeポイントを取得
```python
ord('a') # 97
ord('c') - ord('a') # 2
```

#python
