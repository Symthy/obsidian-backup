
```ts
type Person = { age: number; name: string; alive: boolean };

type Age = Person["age"];  // = number
type I1 = Person["age" | "name"];  // = number | string
type I2 = Person[keyof Person];  // = number | string | boolean

type AliveOrName = "alive" | "name";
type I3 = Person[AliveOrName];  // = string | boolean
```


```ts
const MyArray = [
  { name: "Alice", age: 15 },
  { name: "Bob", age: 23 },
  { name: "Eve", age: 38 },
];

// typeof MyArray => { name: string, age: number }[]
type Person = typeof MyArray[number];  
// = { name: string, age: number } 
type Age = typeof MyArray[number]["age"];  // = number
```