
## 環境構築

Viteの場合
```
npm install @storybook/builder-vite --save-dev
npx storybook init --builder @storybook/builder-vite
```

### v7 VUP

```sh
npx storybook upgrade
```

ref: [遂に来た！Storybookのバージョンを7にするまでに行ったこと](https://qiita.com/KokiSakano/items/a6e291b6292f025bd037)

※ 起動しようとしたら
```
$ npm run storybook

ERR! Error: Invalid value of '@storybook/react' in the 'framework' field of Storybook config.
ERR!
ERR! Please run 'npx storybook@next automigrate' to automatically fix your config.
```
と出たので、以下も実行したらうまくいった
```sh
npx storybook@next automigrate
```

v7 は cli を別途インストール要
```sh
npm i -D @storybook/cli
```

```diff
  "scripts": {
-    "storybook": "start-storybook -p 6006",
-    "build-storybook": "build-storybook",
+    "storybook": "storybook dev -p 6006",
+    "build-storybook": "storybook build",
  }
```

※start-storybook と build-storybook は v7で削除された

ref: https://github.com/storybookjs/storybook/blob/4a532ec8d8922ddf6b7a7e1cc68d88b35377b0a6/MIGRATION.md#from-version-65x-to-700



ref:
- [Vite + React + TypeScript に Vite 用 Storybook を導入する。Storybook は必要だぞ](https://zenn.dev/longbridge/articles/13e65ef71455e4)

### Decorators

- 追加の「レンダリング」機能でストーリーをラップする方法
- preview.ts に設定

ref: 
- https://storybook.js.org/docs/react/writing-stories/decorators


### MSW 設定

[[MSW 導入#セットアップ]]

```typescript
// preview.ts
import { initialize, mswDecorator } from 'msw-storybook-addon';
// :
initialize(); 
// :
export const decorators = [mswDecorator, myDecorator];
// :

export const parameters = {
  // :
  msw: {
    handlers: {
      // 例
      prefectures: mockPrefecturesApiHandler,
      populations: mockPopulationsApiHandler
    }
  },
  // :
};
```

### emotion 設定

storybook に builder-vite を適応して Emotionでスタイリングする際に下記のような文言が className に表示される。 以下の対応が必要

```typescript
// main.ts
import react from '@vitejs/plugin-react';
// :
viteFinal: async (config) => {
  // :
  config.plugins = config.plugins.filter(
    (plugin) => !(Array.isArray(plugin) && plugin[0]?.name.includes('vite:react'))
  );
  config.plugins.push(
    react({
  	  exclude: [/\.stories\.(t|j)sx?$/, /node_modules/],
      jsxImportSource: '@emotion/react',
      babel: {
        plugins: ['@emotion/babel-plugin']
     }
    })
  );
  // :
  return config
}
```

ref: 
- [vite + emotion + storybook で emotion が適応されなくて困ったさん](https://zenn.dev/rabbit/scraps/e9ab3527f49d45)
- [Github Issue: Using the css Prop of @emotion/react and apply babel plugin](https://chot-inc.com/blog/gmwju-vx95/)

### トラブルシュート

以下のエラーが起きるため、react/require-default-props を off にする
```
propType "xxx" is not required, but has no corresponding defaultProps declaration.(eslint: react/require-default-props)
```

※解決策はいくつか挙げられるものの、どれも不十分なため off にする方がよさそう  
- [propType "name" is not required, but has no corresponding defaultProps declaration](https://www.angularfix.com/2022/02/proptype-is-not-required-but-has-no.html)

以下エラーについて
```
[vite]: Rollup failed to resolve import "@mdx-js/react" from "src/stories/Introduction.stories.mdx".
This is most likely unintended because it can break your application at runtime.
If you do want to externalize this module explicitly add it to
`build.rollupOptions.external`
```
- react18 と @mdx-js v2 の相性の問題？ (2022/7/16 時点では未解決)
- mdx 関係はバグがいくつかありそう。明確な回避方法が分からない。
- mdx 使わない方向で対処。mdx ファイルを使わなければ、この問題にはぶつからないため
	- [[Bug] 'mdx' is not exported by node_modules/@mdx-js/react/index.js](https://github.com/storybookjs/builder-vite/issues/421)

以下問題 1 の対処やったが解決せず（参考までに貼り）

- [Storybook が Vite の Builder で動かない問題の回避策](https://zenn.dev/yogarasu/articles/75f7129d7a40bb)

### パス alias 設定

以下の vite.config.ts を流用する方法ではうまくいかず
- [Vite + React + TypeScript に Vite 用 Storybook を導入する。Storybook は必要だぞ](https://zenn.dev/longbridge/articles/13e65ef71455e4#storybook-%E8%B5%B7%E5%8B%95%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB-.main.js-%E3%81%AE%E8%A8%AD%E5%AE%9A)

以下でうまくいった。

```javascript
  viteFinal: async (config) => {
    config.resolve.alias = {
      ...(config.resolve.alias || {}),
      src: path.resolve(__dirname, '../src'),
    };
    return config;
  },
```

以下で vite.config.ts 読み込めるかもしれない。参考: 
- [storybook 起動ファイル .main.js の設定](https://zenn.dev/longbridge/articles/13e65ef71455e4#storybook-%E8%B5%B7%E5%8B%95%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB-.main.js-%E3%81%AE%E8%A8%AD%E5%AE%9A)
- https://github.com/storybookjs/builder-vite/issues/85#issuecomment-900831050

```typescript
const { loadConfigFromFile, mergeConfig } = require("vite");

module.exports = {
	...
  async viteFinal(config, { configType }) {
    const { config: userConfig } = await loadConfigFromFile(
      path.resolve(__dirname, "../vite.config.ts"),
    );

    return mergeConfig(config, {
      ...userConfig,
      // manually specify plugins to avoid conflict
      plugins: [],
    });
  },
};
```

### 設定サンプル

#### main.ts の設定

```typescript
import { fileURLToPath } from 'node:url';
import path from 'node:path';
import react from '@vitejs/plugin-react';
  
export default {
  stories: ['../src/**/*.stories.mdx', '../src/**/*.stories.@(js|jsx|ts|tsx)'],
  addons: [
    '@storybook/addon-links',
    '@storybook/addon-essentials',
    '@storybook/addon-interactions',
    '@storybook/addon-a11y'
  ],
  framework: '@storybook/react',
  core: {
    builder: '@storybook/builder-vite'
  },
  features: {
    storyStoreV7: true,
    interactionsDebugger: true
  },
  staticDirs: ['../public'], // msw用 ref: https://zenn.dev/midorimici/articles/msw-storybook#storybook-%E3%81%AE-static-directory-%E3%82%92%E6%8C%87%E5%AE%9A
  
  viteFinal: async (config) => {
    // vite.config.ts 再利用うまくいかなかったため直接指定
    const __dirname = path.dirname(fileURLToPath(import.meta.url));
    config.resolve.alias = {
      ...(config.resolve.alias || {}),
      src: path.resolve(__dirname, '../src'),
      public: path.resolve(__dirname, '../public')
    };

    // ref: https://zenn.dev/rabbit/scraps/e9ab3527f49d45
    // ref: https://chot-inc.com/blog/gmwju-vx95/
    config.plugins = config.plugins.filter(
      (plugin) => !(Array.isArray(plugin) && plugin[0]?.name.includes('vite:react'))
    );
    config.plugins.push(
      react({
        exclude: [/\.stories\.(t|j)sx?$/, /node_modules/],
        jsxImportSource: '@emotion/react',
        babel: {
          plugins: ['@emotion/babel-plugin']
        }
      })
    );
    config.esbuild = {
      // Fixed: [vite] warning: Top-level "this" will be replaced with undefined since this file is an ECMAScript module
      // https://github.com/vitejs/vite/issues/8644
      logOverride: { 'this-is-undefined-in-esm': 'silent' }
    };
    
    return config;
  }
};
```

#### preview.tsx の設定

```typescript
// Registers the msw addon
import { initialize, mswDecorator } from 'msw-storybook-addon';
import React from 'react';
import { mockPopulationsApiHandler, mockPrefecturesApiHandler } from '../src/mocks/handlers';
import { GlobalStyles } from '../src/libs/global-style'; // 独自のグローバルCSSスタイル

// Initialize MSW
initialize(); // setup はする必要なし

const commonDecorator = (StoryFn: Function) => {
  return (
    <>
      <StoryFn />
      <GlobalStyles />
    </>
  );
};
  
// Provide the MSW addon decorator globally
export const decorators = [mswDecorator, commonDecorator];
export const parameters = {
  actions: { argTypesRegex: '^on[A-Z].*' },
  controls: {
    matchers: {
      color: /(background|color)$/i,
      date: /Date$/
    }
  },
  msw: {
    handlers: {
      // 例
      prefectures: mockPrefecturesApiHandler,
      populations: mockPopulationsApiHandler
    }
  },
  // occur 'ReferenceError: process is not defined' in Chromatic...
  process: {
    env: {
      // 例
      VITE_E2E_MODE: false
    }
  }
};
```



### storybook test 

```
npm i -D @storybook/addon-interactions @storybook/jest @storybook/testing-library @storybook/test-runner jest
```

refs: 
- [Storybook 単体でインタラクションテストを実施する](https://zenn.dev/azukiazusa/articles/storybook-interaction-testing)
- [Interaction Testing with Storybook](https://storybook.js.org/blog/interaction-testing-with-storybook/)

  
### addon

#### a11y

```
npm -i -D @storybook/addon-a11y
```

## storybook でできること

ref: [Vite + React + TypeScript に Vite 用 Storybook を導入する。Storybook は必要だぞ](https://zenn.dev/longbridge/articles/13e65ef71455e4)

### CSF（Component Story Format）

refs:
- [Component Story Format 3.0](https://storybook.js.org/blog/component-story-format-3-0/)
- [Storybook CSF3.0 時代のテストに備える](https://zenn.dev/takepepe/scraps/6f8fb0c9bd6fa9)
- [TypeScript + Storybook CSF3.0 の書き方とユニットテストへの応用](https://zenn.dev/yukishinonome/articles/6bc6e33d579276)

#### CSF3.0：Template.bind ではなく Object で定義できるようになった

CSF2.0

```typescript

import { Story, Meta } from '@storybook/react/types-6-0';
import SimpleFrom, { Props } from './SimpleForm'; // 対象コンポーネント
export default {
  title: 'Atoms/SimpleFrom', // CSF3.0では省略可能
  component: SimpleFrom
} as Meta<Props>;

const Template: Story<Props> = (args) => <SimpleFrom {...args} />;

export const Index = Template.bind({});
Index.args = {
  title: 'お名前フォーム'
};
```

CSF3.0

```typescript
import { ComponentMeta, ComponentStoryObj } from '@storybook/react';
import SimpleForm from './SimpleForm'; // 対象コンポーネント

export default { component: SimpleForm } as ComponentMeta<typeof SimpleForm>;
export const Index: ComponentStoryObj<typeof SimpleForm> = {
  args: {
    title: 'お名前フォーム'
  }
};
```

※ v7 では以下に変更
- `ComponentStory`、`ComponentStoryObj`、`ComponentStoryFn`、`ComponentMeta` は非推奨
- 代わりに、`Meta`、`StoryObj`、`StoryFn`を利用
```typescript
import type { Meta, StoryObj } from '@storybook/react';

import { Button } from './Button';

const meta: Meta<typeof Button> = {
  title: 'Button',
  component: Button,
};

export default meta;
type Story = StoryObj<typeof Button>;

export const Primary: Story = {
  render: () => <Button primary label="Button" />,
};
```

#### play 関数によりインタラクティブストーリーが記載可能

```typescript

import userEvent from '@testing-library/user-event';

export default { component: AccountForm }
export const Empty = {};
export const EmptyError = {
  ...Empty,
  play: () => userEvent.click(screen.getByText('Submit'));
}

export const Filled = {
  ...Empty,
  play: () => {
    userEvent.type(screen.getById('user'), 'shilman@example.com');
    userEvent.type(screen.getById('password'), 'blahblahblah');
  }
}

export const FilledSuccess = {
  ...Filled,  // 再利用も可能
  play: () => {
    Filled.play();
    EmptyError.play();
  }
}
```

  

### Interaction test

refs:
- [Storybook Doc: Interaction tests](https://storybook.js.org/docs/react/writing-tests/interaction-testing)
- [Interaction Testing with Storybook](https://storybook.js.org/blog/interaction-testing-with-storybook/)
- [Storybook 単体でインタラクションテストを実施する](https://zenn.dev/azukiazusa/articles/storybook-interaction-testing)
 
以下を導入すれば、再生と巻き戻しも可能

```
npm i -D @storybook/addon-interactions
```
  
.storybook/main.js

```javascript
  addons: [
    // 他のアドオン,
    '@storybook/addon-interactions'
  ],
  features: {
    interactionsDebugger: true
  },
```

  
- play で実行
- useEvent で コンポーネントとの相互作用をシミュレート
- Jest で Dom Structure を検証

```
// テスト実行
npm run test-storybook
```

### Visual Testing (Github Action)

ref: https://storybook.js.org/tutorials/intro-to-storybook/react/en/deploy/ 

```
npm i -D chromatic
// access to https://www.chromatic.com/start
npx chromatic --project-token=1b98b5ba4c9e
```

refs: 

- [Chromatic と storybook で UI のテストを自動化してみた](https://zenn.dev/kyo9bo/articles/9909ba89c42a77)
- [storybook の VRT で Chromatic を試す](https://zenn.dev/wintyo/articles/6bea3e999ad537)
- [Chromatic のプレビューリンクを自動で PR 上にコメントする](https://zenn.dev/matken/articles/chromatic-preview-comment)

