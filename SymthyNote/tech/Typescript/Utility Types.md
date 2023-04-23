
## Required


```typescript
type Person = {
  firstName: string;
  middleName?: string;
  familyName: string;
};
type RequiredPerson = Required<Person>;
// type RequiredPerson = {
//  firstName: string;
//  middleName: string;
//  familyName: string;
// }
```

## Partial

すべてOptionalにする

```typescript
type Person = {
  firstName: string;
  middleName?: string;
  familyName: string;
};
type RequiredPerson = Required<Person>;
// type RequiredPerson = {
//  firstName?: string;
//  middleName?: string;
//  familyName?: string;
// }
```


### Partialを用いたOptions Objectパターン

引数を省略できる

```typescript
type FindUsersArgs = Partial<User>;

function findUsers({
  surname,
  middleName,
  givenName,
  age,
  address,
  nationality,
}: FindUsersArgs = {}) {
  // ...
}

findUsers();
findUsers({ age: 22 });
```

※通常は以下のようになる
```typescript
function findUsers(
  surname?: string,
  middleName?: string,
  givenName?: string,
  age?: number,
  address?: string,
  nationality?: string
) {
  // ...
}

// 他の引数は`undefined`を入力しなければならない
findUsers(undefined, undefined, undefined, 22);
```

## Readonly


```typescript
type Person = {
  firstName: string;
  middleName?: string;
  familyName: string;
};
type ReadonlyPerson = Readonly<Person>;
// type RequiredPerson = {
//  readonly firstName: string;
//  readonly middleName?: string;
//  readonly familyName: string;
// }
```

- Readonlyの効果は再帰的ではない
	- readonly になるのは直下のプロパティのみ

## Record

```typescript
type StringNumber = Record<string, number>;
const value: StringNumber = { a: 1, b: 2, c: 3 };

type Person = Record<"firstName" | "middleName" | "lastName", string>;
const person: Person = {
  firstName: "Robert",
  middleName: "Cecil",
  lastName: "Martin",
};
```

## Pick

指定したもののみ抽出する

```typescript
// Pick<T, Keys>
type User = {
  surname: string;
  middleName?: string;
  givenName: string;
  age: number;
  address?: string;
  nationality: string;
  createdAt: string;
  updatedAt: string;
};
type Person = Pick<User, "surname" | "middleName" | "givenName">;
// type Person = {
//   surname: string;
//   middleName?: string;
//   givenName: string;
//  };
```

## Omit

指定したものを除外する

```typescript
type User = {
  surname: string;
  middleName?: string;
  givenName: string;
  age: number;
  address?: string;
  nationality: string;
  createdAt: string;
  updatedAt: string;
};
type Optional = "age" | "address" | "nationality" | "createdAt" | "updatedAt";
type Person = Omit<User, Optional>;
// type Person = {
//   surname: string;
//   middleName?: string;
//   givenName: string;
//  };
```

※タイポは教えてくれないため注意

## Exclude

ユニオン型から指定の型を除外

```typescript
// Exclude<T, U>
type Grade = "A" | "B" | "C" | "D" | "E";
type PassGrade = Exclude<Grade, "D" | "E">;
// type PassGrade = "A" | "B" | "C"
```

## Extract

ユニオン型から指定の型を抽出

```typescript
// Extract<T, U>
type Grade = "A" | "B" | "C" | "D" | "E";
type FailGrade = Extract<Grade, "D" | "E">;
// type FailGrade = "D" | "E"

type CommonTypes = Extract<"a" | "b" | "c", "b" | "c" | "d">;
// type CommonTypes = "b" | "c"
```


#Typescript