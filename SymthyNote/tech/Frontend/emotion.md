
# emotion

## install

https://emotion.sh/docs/install

refs:
- [【React】emotion/css の使い方メモ](https://qiita.com/nemutas/items/dac73aa645f27cde92cd)
- [(自称) 日本一わかりやすい emotion のドキュメント](https://qiita.com/cheez921/items/1d13545f8a0ea46beb51)
- [React + TypeScript で Emotion を使う](https://zenn.dev/riemonyamada/articles/e1430eace41f79)
 
storybook への導入には設定が必要  

- [Storybook を Vite で立ち上げる ~Emotion を添えて~](https://chot-inc.com/blog/gmwju-vx95/)

  

参考: 
- [Emotion Doc: Best Practices](https://emotion.sh/docs/best-practices)
- [@emotion/react でコンポーネントの外部からスタイルを受け取る方法](https://zenn.dev/hatchinee/articles/a235edb225fb39)
- [CSS in JS ライブラリ「emotion」のすすめ](https://zenn.dev/itomise/articles/e6386441cac697)
- [Emotion を使いこなす](https://qiita.com/282Haniwa/items/7248bed02a1b5b66579f)
- [Emotion はいいぞ](https://qiita.com/282Haniwa/items/93edc81a884add528593)
- [【お遊び？】Emotion で Utility First してみた](https://qiita.com/honey32/items/c7d4117bbc46c15a273b)

以下のような指定もできるし、配列指定で合成も可能 ⇒ 一部 style を共通化して使用することも可能 

```tsx
<div
  css={[
    css`color: white;`,
    // 1) テーマを引数に取る指定も含められる
    (theme) => css`background-color: ${theme.colors.primary};`,
    // 2) && etc. による条件付き指定も可
    isSelected && css`background-color: red;`,
  ]}
>
```

  

## theme の モードチェンジ

emotion での実現方法
- [Implementing dark mode in next.js with emotion - Topcoder](https://www.topcoder.com/thrive/articles/implementing-dark-mode-in-next-js-with-emotion)
- [Adding Dark Mode to Your React App with Emotion CSS-in-JS](https://levelup.gitconnected.com/adding-dark-mode-to-your-react-app-with-emotion-css-in-js-fc5c0f926838)
- [[React + Typescript] emotion の Theming 機能を使って複数の Theme 切り替えを実装してみた](https://dev.classmethod.jp/articles/react-typescript-emotion-theming/)
- [emotion 公式 Doc：複数 theme サポートしないならば theme は使用すべきでない](https://emotion.sh/docs/best-practices#define-colors-and-other-style-constants-as-javascript-variables)

※ sass で実現する方法もある。ちょっと面倒そう

  

[React で CSS variables でダークモードとライトモードを手軽に切り替える](https://zenn.dev/bom_shibuya/articles/f0a6d7daddfa6f)

  

## グローバル CSS の適用

ref: [Vite+React+Emotion に、リセット CSS を導入する。](https://zenn.dev/longbridge/articles/e2150329bad160)
  

```typescript
import { Global, css } from '@emotion/react';
import sanitize from 'sanitize.css';
import typography from 'sanitize.css/typography.css';
 
const styles = css`
  ${sanitize}
  ${typography}
  body {
    // ここにアプリ固有のベースになるスタイルを書く
  }
`;

export function App() {
  return <Global styles={styles} />;
}
```

- サニタイズCSSは、ノーマライズCSSと同じようにブラウザ間の誤差を調整し、かつlist-style:noneやbox-sizing: border-boxなどのよく使うCSSの指定もしてくれる
- レスポンシブデザインで必ず使う box-sizing: border-box などが予め記述してあるサニタイズ CSS が人気

ref: [【サンプルあり】リセット・ノーマライズ・サニタイズCSSの違いと使い方](https://www.jungleocean.com/programming/190417reset-normalize-sanitize-css)


## その他

[eslint-plugin-react をアップデートしたら、 emotion 関係のエラーが多発した件](https://qiita.com/yuto-ono/items/6642b16f720c9e82fef4)