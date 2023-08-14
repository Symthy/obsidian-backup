
虱潰しに見ていく方法
- 追加する/しない の選択肢を枝分かれ的に見ていく

# Subsets

```python
res = []

def dfs(i,subset):
    if i>=len(nums):
        res.append(subset)
        print(res)
        return

    dfs(i+1,subset+[nums[i]])
    dfs(i+1,subset)

dfs(0,[])
return res
```

```
[[1, 2, 3]]  
[[1, 2, 3], [1, 2]]  
[[1, 2, 3], [1, 2], [1, 3]]  
[[1, 2, 3], [1, 2], [1, 3], [1]]  
[[1, 2, 3], [1, 2], [1, 3], [1], [2, 3]]  
[[1, 2, 3], [1, 2], [1, 3], [1], [2, 3], [2]]  
[[1, 2, 3], [1, 2], [1, 3], [1], [2, 3], [2], [3]]  
[[1, 2, 3], [1, 2], [1, 3], [1], [2, 3], [2], [3], []]
```

https://leetcode.com/problems/subsets/description/

#アルゴリズム