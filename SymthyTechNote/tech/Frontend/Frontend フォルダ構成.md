ref: https://fadamakis.com/a-front-end-application-folder-structure-that-makes-sense-ecc0b690968b

- **components**:   アプリケーション全体で使用されるすべての共有コンポーネント。
- **composables**:   すべての共有コンポーザブル。
- **config**:   アプリケーション構成ファイル。
- **features**:   アプリケーションのすべての機能が含まれています。アプリケーション コードの大部分をここに保持したいと思います。これについては後で詳しく説明します。
- **layouts**:   ページの異なるレイアウト。
- **lib**:   アプリケーションで使用されるさまざまなサードパーティ ライブラリの構成。
- **pages**:   アプリケーションのページ。※すべてのページを 1 か所にまとめておくと非常に便利ですが、ページ内のロジックは最小限に抑える必要がある
- **services**:  共有アプリケーション サービスとプロバイダー。
- **stores**:   グローバル状態ストア。
- **test**:   テスト関連のモック、ヘルパー、ユーティリティ、および構成。
- **types**:   共有 TypeScript 型定義。
- **utils**:   共有ユーティリティ関数。

### features

![[Pasted image 20230806172904.png]]


- **api**: すべてのフェッチ ロジックがここに入力されます。これにより、API と UI が分離されます。
- **components**: 機能固有のコンポーネント。
- **composables**: 機能固有のコンポーザブル。
- **stores**: 状態管理コード。複数のサブモジュールが期待されており、実際に推奨されています。
- **types**: 機能固有の typeScript タイプ定義。
- **index.ts**: これは機能のエントリ ポイントです。**これは機能のパブリック API**として動作し、アプリケーションの他の部分に対してパブリックにする必要があるもののみをエクスポートする必要があります。

上記`**index.ts**`ファイルは各機能の公開APIとなります。別のドメインから何かをインポートする場合は、このファイルを通じてのみ行う必要があります。これにより、循環依存関係が防止され、インポートのソースを見つけやすくなります。


```# 悪い 🚫 🚫 🚫   
import { UserProfile } from  '@/features/profile/components/UserProfile.vue'   
  
# 良い ✅ ✅ ✅   
import { UserProfile } from  '@/features/profile'
```

ESLint ルールを使用して、このパターンを強制できる→ `no-restricted-imports`