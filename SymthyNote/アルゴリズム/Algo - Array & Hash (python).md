https://neetcode.io/roadmap 

# Array & Hash

### ハッシュ化

配列内の要素確認は、ハッシュ化を考える

- 配列内の重複判定： ソートすれば隣接する 
```
[1,2,3,1] -> [1,1,2,3]
```

- 同じ英字で構成された文字列かの判定：文字列をハッシュ化して比較

```
anagram 
-> {'a':3, 'n':2, 'g':1, 'r':1, 'm': 1}
-> hash = [0] * 26  hash[ord(x) - ord('a')] += 1 
// 出現回数をカウント
// pythonなら dict や list は同値比較できる
```

### in と out を 掛けると同じ数字になる

```python
ans = [1] * len(nums)
pre = 1
post = 1
for i in range(len(nums)):
	ans[i] *= pre
	ans[len(nums)-i-1] *= post
	pre = pre * nums[i]
	post = post * nums[len(nums)-i-1]
print(ans)

# input:  [ 1, 2, 3, 4]
# pre:    [ 1, 1, 2, 6]
# post:   [24,12, 4, 1]
# output: [24,12, 8, 6]
```

### 連続する数字の個数カウント

```python
longest = 1
for num in numSet:
	# 連続する数のうち最小のものからカウントする
	if (num - 1) not in numSet:
		length = 1
		while (num + length) in numSet:
			length += 1
		longest = max(longest, length)
print(longest)
```


#アルゴリズム
#python 