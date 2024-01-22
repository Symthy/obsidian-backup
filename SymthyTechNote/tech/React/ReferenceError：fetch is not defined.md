
urql × jest で発生

Jest で fetch を使うには polyfil が必要

 [Jest × React Testing Library × msw で fetch を使ってるコンポーネントのテストでエラーになった](https://chaika.hatenablog.com/entry/2023/08/01/083000)
```sh
npm i -D cross-fetch
```

テストファイルの先頭に以下を入れるだけ
```tsx
import 'cross-fetch/polyfill';
```