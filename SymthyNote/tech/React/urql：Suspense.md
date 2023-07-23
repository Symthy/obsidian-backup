

`useQuery`フックをSuspense対応させる
```tsx
const client = createClient({
  url: `（アクセス先URL）`,
  suspense: true, // ←これを加える
  exchanges: [
    dedupExchange,
    suspenseExchange, // ←これを加える
    cacheExchange,
    fetchExchange,
  ],
  // Cookieを送信したい場合は加える
  fetchOptions: () => ({
    credentials: 'include',
  }),
})
```

- Urqlの`useQuery`フックは通常、`loading`, `error`, `data`という値を持つオブジェクトを返す。
- これが、上で加えた`suspenseExchange`の働きで`loading = true`の状態がなくなり、代わりに`Promise`をthrowするように変化する
	- このコンポーネントを`<React.Suspense>`で囲めば通信完了後に再描画がかかる。Error Boundaryで囲めば通信エラー時の処理をそちらに委譲できるため、コンポーネント内部から`loading`と`error`という状態がなくなり、`data`だけを扱えばよくなる

ref: [GraphQLライブラリをApollo→Relay→Urqlにハシゴした話 - [3つ目：Urql]](https://nulab.com/ja/blog/nulab/graphql-apollo-relay-urql/#3%E3%81%A4%E7%9B%AE%EF%BC%9AUrql)

