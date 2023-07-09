
MSWとは
- ネットワークレベルで API リクエストをインターセプトして mock のデータを返すためのライブラリ

```
npm -i -D msw
npm -i -D msw-storybook-addon
```

## セットアップ

コマンド
```
npx msw init <PUBLIC_DIR> --save
```
※ PUBLIC_DIR は フレームワークによって変わる
https://mswjs.io/docs/getting-started/integrate/browser#where-is-my-public-directory


package.json に以下追加 でも良い

```
"scripts": {
  "init-msw": "msw init public/"
}
```

```
"msw": {
  "workerDirectory": "public"
}
```


## worker 設定

mocks 定義

```
touch src/mocks/handlers.js
touch src/mocks/browser.js
```

```typescript
// vite の場合
if (import.meta.env.DEV) {
  const { worker } = require('./mocks/browser')
  worker.start().then(() => {
    worker.printHandlers();
  })
}
```

## REST 

browser.ts
```typescript
import { setupWorker } from 'msw'
import { handlers } from './handlers'

export const worker = setupWorker(...handlers)
```
特殊版
```typescript
// eslint-disable-next-line import/no-extraneous-dependencies
import { setupWorker, rest as baseRest, SetupWorkerApi } from 'msw';
import { handlers } from './handlers';

export const worker = setupWorker(...handlers);
export const rest = baseRest;

// Cypress 等 の e2e 起動判定に window がいるようなケース用
type MswHolder = {
  msw: {
    worker: SetupWorkerApi;
    rest: typeof rest;
  };
};

(window as Window & typeof globalThis & MswHolder).msw = { worker, rest };
```
handlers.ts
```typescript
import { rest } from 'msw';
import { mockPrefecturesApi } from './resolvers/mockPrefecturesApi';

export const mockPrefecturesApiHandler = rest.get("https://opendata.resas-portal.go.jp/api/v1/prefectures", mockPrefecturesApi);
```
resolvers/mockXxx.ts
```typescript
import { ResponseResolver, MockedRequest, restContext } from 'msw';
export const mockPrefecturesApi: ResponseResolver<MockedRequest, typeof restContext> = (req, res, ctx) =>
  res(
    ctx.status(200),
    ctx.json({ ... })
  );
```

ref:
- [Mock Service Worker: GETTING STARED](https://mswjs.io/docs/getting-started)
- [React に MSW を導入する手順](https://zenn.dev/higuchimakoto/articles/d9865193910046)
- [react+vite を playwright+msw で自動テストする](https://zenn.dev/dyoshikawa/articles/07ab82a5cbcde0)
- [Mock Service Worker で開発用のモックAPIを作る](https://zenn.dev/ryo_kawamata/articles/mock-api-server-with-msw)

## GraphQL

