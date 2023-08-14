
```typescript
type Options = {
  x: number;
  y: number;
  z: number;
};

function func({ x, y, z }: Options) {
  // ...
}
```

-   引数の値が何を指すのか分かりやすい
-   引数追加時に古いコードを壊さない
-   デフォルト引数が省略できる

python の キーワード引数が同じことができる
```python
def func(x, y, z):
print(x, y, z)

func(x=1, y=2, z=3) # => 1 2 3
```


ref: [キーワード引数とOptions Objectパターン](https://typescriptbook.jp/reference/functions/keyword-arguments-and-options-object-pattern)

#Typescript