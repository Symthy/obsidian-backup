
```python
# 素数を判定する関数
def isprime(N):
    if N < 2:
        return False
    i = 2
    while i * i <= N:
        if N % i == 0:
            return False
        i += 1
    return True

# 1 から 10 までが素数かどうか
for n in range(1, 11):
    res = 'prime' if isprime(n) else 'not'
    print('{}: {}'.format(n, res))
```

#アルゴリズム 
#python 