ref: https://www.typescriptlang.org/docs/handbook/2/conditional-types.html

```ts
interface Animal {
  live(): void;
}

interface Dog extends Animal {
  woof(): void;
}

type Example1 = Dog extends Animal ? number : string;  // = number
type Example2 = RegExp extends Animal ? number: string  // = string
```

上記のような使い方はしないが
ジェネリクスとの使用で真価を発揮する

```ts
interface IdLabel {
  id: number 
}
interface NameLabel {
  name: string
}

function createLabel(id: number): IdLabel;
function createLabel(name: string): NameLabel;
function createLabel(nameOrId: string | number): IdLabel | NameLabel;
// ↓
type NameOrId<T extends number | string> = T extends number ? IdLabel : NameLabel
function createLabel<T extends number | string>(idOrName: T): NameOrId<T> {
  throw "unimplemented";
}
const a = createLabel("typescript");  // a: NameLabel
const b = createLabel(2.8);  // b: IdLabel
```


### Conditional Type Constraints
条件付きタイプ制約

```ts
type MessageOf<T extends { message: unknown }> = T["message"];
interface Email {
  message: string;
}

type EmailMessageContents = MessageOf<Email>;  // = string
```

```ts
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never;
interface Email {
  message: string;
}
interface Dog {
  bark(): void;
}

type EmailMessageContents = MessageOf<Email>;  // = string
type DogMessageContents = MessageOf<Dog>;   // = never
```

```ts
type Flattern<T> = T extends any[] ? T[number] : T

type Str = Flattern<string[]>  // = string
type Num = Flattern<number>  // = number
```

### Inferring Within Conditional Types
条件型内の推論

```ts
type ReturnType<T> = T extends (...args: never[]) => infer R ? R : never;

type Num = ReturnType<() => number>;  // = number
type Str = ReturnType<(x: string) => string>;  // = string
type Bools = ReturnType<(a: boolean, b: boolean) => boolean[]>;  // boolean[]
```

## Distributive Conditional Types
分配条件型

```ts
type ToArray<T> = T extends any ? T[] : never;

type StrArrOrNumArr = ToArray<string | number>;  // = string[] | number[]

// 分けない場合
type ToArrayNonDist<T> = [T] extends [any] ? T[] : never;
type StrArrOrNumArr = ToArrayNonDist<string | number>;  // = (string | number)[]
```