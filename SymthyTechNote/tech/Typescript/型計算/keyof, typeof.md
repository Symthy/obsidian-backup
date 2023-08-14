## keyof

```ts
type Point = { x: number; y: number };
type P = keyof Point;
const p1: P = 'x';
const p2: P = 'y';

type Arrayish = { [n: number]: unknown };
const arr: Arrayish = { 1: 'x' };
type A = keyof Arrayish;  // type A = number
const a1: A = 1;

type Mapish = { [k: string]: boolean };
type M = keyof Mapish;  // type M = string | number
const m1 = 1
const m2 = 'x'
```

  JavaScriptのオブジェクトキーは常に文字列に強制される。`obj[0]` は常に `obj["0"]` と同じ。
 key が 数字なら number になる（index）。だから `type M = string | number` になる

## typeof

```ts
let s = "hello";
let n: typeof s;  // string
```

関数を扱う場合は注意が必要

```ts
type Predicate = (x: unknown) => boolean;
type K = ReturnType<Predicate>;

function f() {
  return { x: 10, y: 3 };
}
// type P = ReturnType<f>;  // error
type P = ReturnType<typeof f>;
```