
```ts
type TupleToObject<T extends readonly any[]> = {
  [P in T[number]]: P
}

const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const
TupleToObject<typeof tuple>
// { 'tesla': 'tesla'; 'model 3': 'model 3'; 'model X': 'model X'; 'model Y': 'model Y' }
```

`T[number]` の number は index 