
https://hackernoon.com/mastering-solid-principles-like-the-back-of-your-hand-in-just-8-minutes?source=rss

## 単一責任の原則

Bad : 複数の責任を持つコンポーネント
```tsx
const Products = () => {
    return (
        <div className="products">
            {products.map((product) => (
                <div key={product?.id} className="product">
                    <h3>{product?.name}</h3>
                    <p>${product?.price}</p>
                </div>
            ))}
        </div>
    );
};
```

Good : 責任をより小さな構成要素に分ける
```tsx
import Product from './Product';
import products from '../../data/products.json';

const Products = () => {
    return (
        <div className="products">
            {products.map((product) => (
                <Product key={product?.id} product={product} />
            ))}
        </div>
    );
};

// Product.js 商品詳細のレンダリングを担当する別コンポーネント
const Product = ({ product }) => {
    return (
        <div className="product">
            <h3>{product?.name}</h3>
            <p>${product?.price}</p>
        </div>
    );
};
```

## Open-Closed 原則

コンポーネントは拡張に対してはオープン（新しい動作や機能を追加できる）であるべきだが、変更に対してはクローズ（既存のコードは変更されないままであるべき）であるべき

Bad
```tsx
// Button.js  既存コンポーネント(修正前)
const Button = ({ text, onClick }) => {
  return (
    <button onClick={onClick}>
      {text}
    </button>
  );
}

// Button.js  Bad:既存に新規prop追加
const Button = ({ text, onClick, icon }) => {
  return (
    <button onClick={onClick}>
      <i className={icon} />
      <span>{text}</span>
    </button>
  );
}

// Home.js
// 👇 Avoid: 既存のコンポーネントの prop の変更を余儀なくされる
const Home = () => {
  const handleClick= () => {};

  return (
    <div>
      {/* ❌ Avoid this */}
      <Button text="Submit" onClick={handleClick} icon="fas fa-arrow-right" /> 
    </div>
  );
}
```

Good
```tsx
// Button.js  既存
const Button = ({ text, onClick }) => {
  return (
    <button onClick={onClick}>
      {text}
    </button>
  );
}

// IconButton.js  Good
const IconButton = ({ text, icon, onClick }) => {
  return (
    <button onClick={onClick}>
      <i className={icon} />
      <span>{text}</span>
    </button>
  );
}

// Home.js
const Home = () => {
  const handleClick = () => {
    // Handle button click event
  }
  return (
    <div>
      <Button text="Submit" onClick={handleClick} />
      <IconButton text="Submit" icon="fas fa-heart" onClick={handleClick} />
    </div>
  );
}
```


## リスコフの置換原理

Reactコンポーネントの文脈では、LSPは、派生コンポーネントが、アプリケーションの正しさや動作に影響を与えることなく、ベースコンポーネントを代用できるようにすべきだという考えを推進

Bad
```tsx
// Bad: select の機能を制限してしまう
const BadCustomSelect = ({ value, iconClassName, handleChange }) => {
  return (
    <div>
      <i className={iconClassName}></i>
      <select value={value} onChange={handleChange}>
        <options value={1}>One</options>
        <options value={2}>Two</options>
        <options value={3}>Three</options>
      </select>
    </div>
  );
};

const LiskovSubstitutionPrinciple = () => {
  const [value, setValue] = useState(1);

  const handleChange = (event) => {
    setValue(event.target.value);
  };

  return (
    <div>
      {/** ❌ Avoid this */}
      <BadCustomSelect value={value} handleChange={handleChange} />
    </div>
  );
```

Good
```tsx
// select 要素の特性を継承
const CustomSelect = ({ value, iconClassName, handleChange, ...props }) => {
  return (
    <div>
      <i className={iconClassName}></i>
      <select value={value} onChange={handleChange} {...props}>
        <options value={1}>One</options>
        <options value={2}>Two</options>
        <options value={3}>Three</options>
      </select>
    </div>
  );
};

const LiskovSubstitutionPrinciple = () => {
  const [value, setValue] = useState(1);

  const handleChange = (event) => {
    setValue(event.target.value);
  };

  return (
    <div>
      {/* CustomSelectコンポーネントは、リスコフの置換原理に従っている */}
      <CustomSelect
        value={value}
        handleChange={handleChange}
        defaultValue={1}
      />
    </div>
  );
};
```


## インターフェース分離の原則

インターフェイスは過度に広範で、クライアントに不必要な機能の実装を強いるのではなく、特定のクライアントの要求に合わせて焦点を絞るべきであると提案

Bad
```tsx
// product に依存してしまう。不必要な依存性と複雑さをもたらす
const ProductThumbnailURL = ({ product }) => {
  return (
    <div>
      <img src={product.imageURL} alt={product.name} />
    </div>
  );
};

// Bad
const Product = ({ product }) => {
  return (
    <div>
      <ProductThumbnailURL product={product} />
      <h4>{product?.name}</h4>
      <p>{product?.description}</p>
      <p>{product?.price}</p>
    </div>
  );
};

const Products = () => {
  return (
    <div>
      {products.map((product) => (
        <Product key={product.id} product={product} />
      ))}
    </div>
  );
}
```

Good
```tsx
// product に依存しない
const ProductThumbnailURL = ({ imageURL, alt }) => {
  return (
    <div>
      <img src={imageURL} alt={alt} />
    </div>
  );
};

const Product = ({ product }) => {
  return (
    <div>
      <ProductThumbnailURL imageURL={product.imageURL} alt={product.name} />
      <h4>{product?.name}</h4>
      <p>{product?.description}</p>
      <p>{product?.price}</p>
    </div>
  );
};

const Products = () => {
  return (
    <div>
      {products.map((product) => (
        <Product key={product.id} product={product} />
      ))}
    </div>
  );
};
```


## 依存関係逆転の原則

高レベルのコンポーネントは低レベルのコンポーネントに依存すべきではないことを強調している。この原則は、疎結合とモジュール性を促進し、ソフトウェア・システムのメンテナンスを容易にする

Bad
```tsx
// 抽象ではなく、具体になっている。CustomFormは子コンポーネントに密結合
// DependencyInversionPrinciple(具体) -> CustomForm(具体) -> children(具体)
const CustomForm = ({ children }) => {
  const handleSubmit = () => {
    // submit operations (ロジックがここにある)
  };
  return <form onSubmit={handleSubmit}>{children}</form>;
};

const DependencyInversionPrinciple = () => {
  const [email, setEmail] = useState();

  const handleChange = (event) => {
    setEmail(event.target.value);
  };

  const handleFormSubmit = (event) => {
    // submit business logic here
  };

  return (
    <div>
      {/** ❌ Avoid: tightly coupled and hard to change */}
      <BadCustomForm>
        <input
          type="email"
          value={email}
          onChange={handleChange}
          name="email"
        />
      </BadCustomForm>
    </div>
  );
};
```

Good
- フォームの抽象化として機能するAbstractFormコンポーネントを導入
- onSubmit関数をpropとして受け取り、フォームの送信を処理
- 上位コンポーネントを修正することなく、フォームの動作を簡単に入れ替えたり拡張したりできる
```tsx
// DependencyInversionPrinciple(具体) <- AbstractForm(抽象) -> children(具体)
const AbstractForm = ({ children, onSubmit }) => {
  const handleSubmit = (event) => {
    event.preventDefault();
    onSubmit();
  };

  return <form onSubmit={handleSubmit}>{children}</form>;
};

const DependencyInversionPrinciple = () => {
  const [email, setEmail] = useState();

  const handleChange = (event) => {
    setEmail(event.target.value);
  };

  const handleFormSubmit = () => {
    // submit business logic here
  };

  return (
    <div>
      <AbstractForm onSubmit={handleFormSubmit}>
        <input
          type="email"
          value={email}
          onChange={handleChange}
          name="email"
        />
        <button type="submit">Submit</button>
      </AbstractForm>
    </div>
  );
};
```