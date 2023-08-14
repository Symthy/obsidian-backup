
ref: [6 Advanced TypeScript tricks for Clean Code(https://medium.com/@mvsg/6-advanced-typescript-tricks-for-clean-code-90cee774dbf3)

## 1. Advanced types

### Mapped Types

既存の型のプロパティを反復処理し、変換を適用して新しい型を作成

```typescript
type Readonly<T> = {  
  readonly [P in keyof T]: T[P];  
};  
  
interface Point {  
  x: number;  
  y: number;  
}  
  
type ReadonlyPoint = Readonly<Point>;
// {
//    readonly x: number;
//    readonly y: number;
// }

// type PointKey = keyof Point
// type PointKey = "x" | "y";
```

Mapped Typesには追加のプロパティが書けない。プロパティを追加する場合はインターセクション型で合成

```typescript
type KeyValues = {
  [K in string]: string;
};

type Name = {
  name: string; // 追加のプロパティ
};

type KeyValuesAndName = KeyValues & Name;
```

### Conditional Types

条件に基づいて新しい型を作成する。 extendsキーワードを型制約として使用

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
```

## 2. Decorators

- クラス、メソッド、プロパティ、パラメータの動作にメタデータを追加したり、変更したり、拡張したりすることができる強力な機能。
- クラス定義、メソッド定義、アクセサ定義、プロパティ定義、パラメータ定義の観察、変更、置換に使用できる高次関数

Class Decorators

```typescript
function LogClass(target: Function) {  
  console.log(`Class ${target.name} was defined.`);  
}  
  
@LogClass  
class MyClass {  
  constructor() {}  
}
```

Method Decorators

```typescript
function LogMethod(target: any, key: string, descriptor: PropertyDescriptor) {  
  console.log(`Method ${key} was called.`);  
}  
  
class MyClass {  
  @LogMethod  
  myMethod() {  
    console.log("Inside myMethod.");  
  }  
}  
  
const instance = new MyClass();  
instance.myMethod();
```

Property Decorators

```typescript
function DefaultValue(value: any) {  
  return (target: any, key: string) => {  
    target[key] = value;  
  };  
}  
  
class MyClass {  
  @DefaultValue(42)  
  myProperty: number;  
}  
  
const instance = new MyClass();  
console.log(instance.myProperty); // Output: 42
```

Parameter Decorators：パラメータのインデックスと名前が引数となる
```typescript
function LogParameter(target: any, key: string, parameterIndex: number) {  
  console.log(`Parameter at index ${parameterIndex} of method ${key} was called.`);  
}  
  
class MyClass {  
  myMethod(@LogParameter value: number) {  
    console.log(`Inside myMethod with value ${value}.`);  
  }  
}  
  
const instance = new MyClass();  
instance.myMethod(5);
```

Field Decorators

```typescript
function addOne<T>(target: undefined,  context: ClassFieldDecoratorContext<T, number>) {  
  return function (this: T, value: number) {  
    console.log('addOne: ', value); // 3  
    return value + 1;  
  };  
}  
  
function addTwo<T>(target: undefined, context: ClassFieldDecoratorContext<T, number>) {  
  return function (this: T, value: number) {  
    console.log('addTwo: ', value); // 1  
    return value + 2;  
  };  
}

class MyClass {  
  @addOne  
  @addTwo  
  x = 1;  
}  
console.log(new MyClass().x); // 4
```

Auto-Accessor Decorators

```typescript
class C {
  accessor x = 1;  
}  

/* Same */
// class C {  
//   #x = 1;  
//   get x() {  
//     return this.#x;  
//   }
//   set x(val) {  
//     this.#x = val;  
//   }  
// }
```

他参考：
- [A Quick Guide to TypeScript 5.0 Decorators](https://medium.com/@islizeqiang/a-quick-guide-to-typescript-5-0-decorators-d06cabe09e8c)

## 3. Namespaces

名前空間は、関連するコードを整理してグループ化するための方法

```typescript
namespace MyNamespace {  
  export class MyClass {  
    constructor(public value: number) {}  
  
    displayValue() {  
      console.log(`The value is: ${this.value}`);  
    }  
  }  
}


// Using the fully-qualified name  
const instance1 = new MyNamespace.MyClass(5);  
instance1.displayValue(); // Output: The value is: 5  
  
// Using a namespace import  
import MyClass = MyNamespace.MyClass;  
const instance2 = new MyClass(10);  
instance2.displayValue(); // Output: The value is: 10
```

ネストも可能

```typescript
namespace OuterNamespace {  
  export namespace InnerNamespace {  
    export class MyClass {  
      constructor(public value: number) {}  
      displayValue() {  
        console.log(`The value is: ${this.value}`);  
      }
    }
  }
}
  
// Using the fully-qualified name  
const instance = new OuterNamespace.InnerNamespace.MyClass(15);  
instance.displayValue(); // Output: The value is: 15
```


## 4. Mixins

- TypeScriptのMixinは、Mixinクラスと呼ばれる複数の小さな部品からクラスを構成する方法
- 異なるクラス間で振る舞いを再利用・共有することができ、モジュール化とコードの再利用を促進する

```typescript
// define
class TimestampMixin<TBase extends new (...args: any[]) => any>(Base: TBase) {  
  constructor(...args: any[]) {  
    super(...args);  
  }  
  
  getTimestamp() {
    return new Date();  
  }  
}

// using
class MyBaseClass {  
  constructor(public value: number) {}  
  displayValue() {  
    console.log(`The value is: ${this.value}`);  
  }  
}  
class MyMixedClass extends TimestampMixin(MyBaseClass) {  
  constructor(value: number) {  
    super(value);  
  }  
}

const instance = new MyMixedClass(42);  
instance.displayValue(); // Output: The value is: 42  
console.log(`The timestamp is: ${instance.getTimestamp()}`); // Output: The timestamp is: [current date and time]
```

## 5. Type Guards

[[型ガード]]

## 6. Utility Types

[[Utility Types]]

#Typescript