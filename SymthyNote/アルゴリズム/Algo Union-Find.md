
無向グラフの連結性を簡潔に管理できる

縮小版
```python
class UnionFind():
    def __init__(self, n):
        self.n = n
        self.parents = [-1] * n 
          # 各要素の親要素の番号を格納するリスト
          # 要素が根（ルート）の場合は`-(そのグループの要素数)`を格納する

    # 要素`x`が属するグループの根を返す
    def find(self, x):
        if self.parents[x] < 0:
            return x
        self.parents[x] = self.find(self.parents[x])
        return self.parents[x]

    # 要素`x`が属するグループと要素`y`が属するグループとを併合する
    def union(self, x, y):
        x = self.find(x)
        y = self.find(y)
        if x == y:
            return False  # 併合済み（併合しない）
        if self.parents[x] > self.parents[y]:
            x, y = y, x
        self.parents[x] += self.parents[y]
        self.parents[y] = x
        return True # 併合する
```


全量
```python
from collections import defaultdict

class UnionFind():
    def __init__(self, n):
        self.n = n
        self.parents = [-1] * n 
          # 各要素の親要素の番号を格納するリスト
          # 要素が根（ルート）の場合は`-(そのグループの要素数)`を格納する

    # 要素`x`が属するグループの根を返す
    def find(self, x):
        if self.parents[x] < 0:
            return x
        else:
            self.parents[x] = self.find(self.parents[x])
            return self.parents[x]

    # 要素`x`が属するグループと要素`y`が属するグループとを併合する
    def union(self, x, y):
        x = self.find(x)
        y = self.find(y)

        if x == y:
            return

        if self.parents[x] > self.parents[y]:
            x, y = y, x

        self.parents[x] += self.parents[y]
        self.parents[y] = x

    # 要素`x`が属するグループのサイズ（要素数）を返す
    def size(self, x):
        return -self.parents[self.find(x)]

    # 要素`x`, `y`が同じグループに属するかどうかを返す
    def same(self, x, y):
        return self.find(x) == self.find(y)

    # 要素`x`が属するグループに属する要素をリストで返す
    def members(self, x):
        root = self.find(x)
        return [i for i in range(self.n) if self.find(i) == root]

    # すべての根の要素をリストで返す
    def roots(self):
        return [i for i, x in enumerate(self.parents) if x < 0]

    # グループの数を返す
    def group_count(self):
        return len(self.roots())

    # {ルート要素: [そのグループに含まれる要素のリスト], ...}`の`defaultdict`を返す
    def all_group_members(self):
        group_members = defaultdict(list)
        for member in range(self.n):
            group_members[self.find(member)].append(member)
        return group_members

    def __str__(self):
        return '\n'.join(f'{r}: {m}' for r, m in self.all_group_members().items())
```

https://note.nkmk.me/python-union-find/


#アルゴリズム/Union-Find