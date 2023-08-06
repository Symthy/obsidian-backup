
ref: [【TypeScript】 inferに詳しくなろう](https://qiita.com/ehika/items/8f41d4a3c8f9df4af9c3)

- infer = 推論
- TypeScriptのextendsを使うと、型での条件分岐が可能になる。
- inferはその条件分岐で推論された型を指すときに用いることができる

```ts
type Unpacked<T> = T extends (infer U)[]
  ? U
  : T extends (...args: any[]) => infer U
  ? U
  : T extends Promise<infer U>
  ? U
  : T;
```

- `string[]` は string
- `(a: any) => boolean` は boolean
- `Promise<number>` は number