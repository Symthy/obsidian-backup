

以下４つの比較がある。 ref:[E2E テストツール Autify を使うまでの話](https://teamspirit.hatenablog.com/entry/2020/04/17/150000)
- TestCafe
- WebDriverIO
- Cypress.io
- Autify (有料)

以下３つの比較がある。 ref: [E2E テストフレームワークはどれを選べばいいんじゃい！](https://zenn.dev/taiga533/articles/f6e1ef07a8676e)

- Cypress
- Playwright
- CodeceptJS

触ったのは以下２つ
- Cypress
- Playwright

## Autify

Autify の良いところ

- 習得が容易
  - 非エンジニアでも自動テストが作成できる（AI による No Code ベース）
  - 簡単に作れるので、UI 変更への対応も比較的楽
- お金がかかる（個人では手が出しづらい）

## E2E テストフレームワーク

refs:
- [E2E テストフレームワークの Cypress に入門した](https://zenn.dev/manalink/articles/manalink-cypress-introduce) 
- [ブラウザ拡張の E2E テストを検討してみた（Playwright、Puppeteer、Cypress）](https://tech.techtouch.jp/entry/e2e-testing-tool#:~:text=Playwright%20%E3%81%A8%20Puppeteer%20%E3%81%AF%E3%80%8C%E3%83%96%E3%83%A9%E3%82%A6%E3%82%B6,%E3%82%92%E6%89%B1%E3%81%86%E3%81%93%E3%81%A8%E3%81%8C%E3%81%A7%E3%81%8D%E3%81%BE%E3%81%9B%E3%82%93%E3%80%82)

### Playwright

- 複数のページ（タブ）を単一のテストケースでサポートできること

### Cypress

- 複数タブについては Cypress はアーキテクチャ上、永久にサポート不可能
- Component Testing というコンポーネント単位で E2E できるという衝撃的な新機能

Cypress は書きにくいらしい
- ref: [本気で考える React のベストプラクティス！bulletproof-react2022](https://zenn.dev/t_keshi/articles/bulletproof-react-2022#e2e%E3%83%86%E3%82%B9%E3%83%88%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)