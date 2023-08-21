
ref: [フロントエンドのデザインパターン](https://zenn.dev/morinokami/books/learning-patterns-1)

## コンポーネント

### [プロバイダパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/provider-pattern)****

- 代表例：Context 
- グローバルなデータの共有に非常に有効
- ユースケース：UI テーマに関するステートを多くのコンポーネントで共有すること
	- テーマ：ダークモード

Pros
- コンポーネントの各レイヤーに手動でデータを渡していくことなく、多くのコンポーネントにデータを届けることができる
	- コードをリファクタリングするときに、誤ってバグを導入するリスクが減る
	- アンチパターンともいえる prop のバケツリレー に対処する必要がなくなる
- ある種のグローバルな状態を保持することが、各コンポーネントがそのグローバルな状態にアクセスできるようになる

Cons
- 特定のケースでは、プロバイダパターンを使いすぎるとパフォーマンスの問題が発生することがある
	- コンテクストを消費するすべてのコンポーネントは、ステートが変化するたびに再レンダリングする
- 大規模なアプリケーションでは、頻繁に更新される値を多くのコンポーネントに渡すと、パフォーマンスに悪影響が出る可能性がある
	- 更新される可能性のある不要な値を含むプロバイダをコンポーネントが消費しないよう、個別のユースケースごとに複数のプロバイダを作成する必要がある

### [コンテナ・プレゼンテーションパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/presentational-container-pattern)

- アプリケーションロジックの処理とビューを分離
	- コンテナコンポーネントは、hooks に置き換えられる

### [オブザーバパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/observer-pattern)

logger や Toastify で使用

Pros
- オブザーバパターンは、**関心の分離**と単一責任の原則を実現する方法
- Observer オブジェクトは Observable オブジェクトと密結合しておらず、いつでも結合 (あるいは疎結合化) することができる。
- Observable オブジェクトはイベントの監視に責任をもつのに対し、Observer は受け取ったデータを処理するだけ

Cons
- Observer が複雑になりすぎると、すべての Subscriber に通知する際にパフォーマンスの問題が発生する可能性がある

### [メディエータ・ミドルウェアパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/mediator-pattern)

中央のメディエータオブジェクトを介してコンポーネント間のやり取りを処理する

コンポーネント自体ではなく、ステートのオブジェクトに使用する感じ

メディエータパターン
- すべての通信が中央のメディエータを経由して流れるようにすることで、オブジェクト間の多対多の関係を単純化する
- 実例：チャットルーム。チャットルームのユーザーは、お互いに直接話すことはありません。その代わり、チャットルームサーバーがメディエータとしてユーザーを仲介
	- ユーザに チャットルームのオブジェクトを持たせる

![[Pasted image 20230815140124.png]]

### [HOC パターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/hoc-pattern)

複数のコンポーネントが同じデータにアクセスする場合や、同じロジックを含む場合に、コンポーネントのロジックを再利用できる

- 高階コンポーネント (HOC、Higher Order Component) は、他のコンポーネントを受け取るコンポーネント
- アプリケーション全体でコンポーネントロジックを再利用する
- 複数の高階コンポーネントを**合成する** (compose) こともできる → フックに置き換えられる

```tsx
import React, { useEffect, useState } from "react";

export default function withLoader(Element, url) {
  return (props) => {
    const [data, setData] = useState(null);

    useEffect(() => {
      async function getData() {
        const res = await fetch(url);
        const data = await res.json();
        setData(data);
      }

      getData();
    }, []);

    if (!data) {
      return <div>Loading...</div>;
    }

    return <Element {...props} data={data} />;
  };
}
```

一般に、React フックは HOC パターンを置き換えるものではない。 多くの場合はフックで十分であり、これによりツリー内のネストを減らすことができる

**HOC が最適なユースケース**:

- 同一の、カスタマイズ不要なロジックが、アプリケーション全体で多くのコンポーネントによって使われる
- コンポーネントは、追加のカスタムロジックなしで、独立して動作する

**フックが最適なユースケース**:

- 各コンポーネントごとにロジックをカスタマイズしたい
- ロジックはアプリケーション全体で使われず、1 つまたはいくつかのコンポーネントだけが使用する
- ロジックによってコンポーネントに多くのプロパティが追加される

Pros
- HOC パターンを使うと、再利用したいロジックを一カ所にまとめておくことができる
	- バグを発生させる可能性のあるコードがアプリケーション内で重複した結果、意図せずしてアプリケーション全体にバグを拡散してしまうようなリスクを減らすことができる
	- ロジックを一箇所にまとめることは、`DRY` なコードを維持し、**関心の分離**を実現しやすくすることにもつながる

Cons
- HOC が要素に渡す prop の名前が、他の名前と衝突する可能性がある（同名のpropsが上書きされる）
	- 回避策：prop の名前を変更するか、props をマージする
- ラップされた要素に props を渡すような HOC を複数**組み合わせて**使うと、どの prop がどの HOC に由来するのかを把握することが困難な場合がある。
	- アプリケーションのデバッグとスケーリングを難しくする原因となる

```tsx
function withStyles(Component) {
  return props => {
    const style = {
      padding: '0.2rem',
      margin: '1rem',
      ...props.style  // props をマージ
    }

    return <Component style={style} {...props} />
  }
}

const Button = () = <button style={{ color: 'red' }}>Click me!</button>
const StyledButton = withStyles(Button)
```


### [レンダープロップパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/render-props-pattern)

- HOCとは別の、コンポーネントを再利用しやすくするもう一つの方法
- props を 通じて JSX 要素をコンポーネントに渡す
- `render`プロップに異なる値を渡して、何度でも使用することができる

```tsx
<Title render={() => <h1>I am a render prop!</h1>} />
const Title = props => props.render();
```

```tsx
function Component(props) {
  const data = { ... } return props.render(data) 
}
<Component render={data => <ChildComponent data={data} />} />
```

#### ステートを持ち上げる

- ユーザーの入力値を 子コンポーネントから利用できるようにするためは、ステートを上位に移動する必要がある
- 多くの子コンポーネントを扱う大規模なアプリケーションでは、**ステートを持ち上げる**ことが難しい場合がある
	- ステートを変更するたびに、データを受け取らないものも含めて、すべての子コンポーネントが再レンダリングされる可能性があるため、アプリケーションのパフォーマンスに悪影響が出る可能性がある

```tsx
// Bad
function Input({ value, handleChange }) {
  return <input value={value} onChange={e => handleChange(e.target.value)} />;
}

export default function App() {
  const [value, setValue] = useState("");

  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input value={value} handleChange={setValue} />
      <Kelvin value={value} />
      <Fahrenheit value={value} />
    </div>
  );
}
```

```tsx
function Input(props) {
  const [value, setValue] = useState("");

  return (
    <>
      <input
        type="text"
        value={value}
        onChange={e => setValue(e.target.value)}
        placeholder="Temp in °C"
      />
      {props.render(value)}
    </>
  );
}

export default function App() {
  return (
    <div className="App">
      <h1>☃️ Temperature Converter 🌞</h1>
      <Input
        render={value => (
          <>
            <Kelvin value={value} />
            <Fahrenheit value={value} />
          </>
        )}
      />
    </div>
  );
}
```

### [フックパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/hooks-pattern)

### [複合パターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/compound-pattern)

- 多くのアプリケーションは、互いに関連し合うコンポーネントをもつ。
- それらは共有された状態を通じて互いに依存し合い、ロジックを共有する。
	- たとえば、`select`、ドロップダウン、メニューなどのコンポーネントがこれにあたる。
	- **複合コンポーネントパターン** (compound component pattern) を使うと、あるタスクを実行するために連携するコンポーネントを作成することができる



## JS
### [シングルトンパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/singleton-pattern)

### [プロキシパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/proxy-pattern)

プロキシ
- オブジェクトの振る舞いを制御するための強力な手段
- ユースケース：バリデーション、書式設定、通知、デバッグなど、様々

```js
const person = {
  name: "John Doe",
  age: 42,
  nationality: "American"
};

const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${Reflect.get(obj, prop)}`);
  },
  set: (obj, prop, value) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    return Reflect.set(obj, prop, value);
  }
});

personProxy.name;
personProxy.age = 43;
personProxy.name = "Jane Doe";
```

![[Pasted image 20230815131935.png]]
### [プロトタイプパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/prototype-pattern)

### [モジュールパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/module-pattern)

### [ミックスインパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/mixin-pattern)

React では使用非推奨
### [フライウェイトパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/flyweight-pattern)

フライウェイトパターンは、膨大な数のオブジェクトを作成することで、使用可能な **RAM** をすべて消費してしまうような場合に役に立ちます。消費されるメモリの量を最小限に抑えることができる。

JavaScript では[プロトタイプ継承](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)によって、この問題を簡単に解決することができます。現在では、ハードウェアは GB 単位の **RAM** をもっているため、フライウェイトパターンの重要性は低くなっている。
### [ファクトリパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/factory-pattern)

Pros
- ファクトリパターンは、同じプロパティを共有する小さなオブジェクトを複数作成する場合に便利

Cons
- 毎回新しいオブジェクトを作成するよりも、新しいインスタンスを作成する方が、メモリ効率が良い場合が多い
### [コマンドパターン](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/command-pattern)


### 参考 
[Learning JavaScript Design Patterns](https://zenn.dev/morinokami/books/learning-patterns-1/viewer/classic-design-patterns)
