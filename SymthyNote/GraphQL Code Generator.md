# GraphQL Code Generator

公式ドキュメント: 
- 設定ファイル： https://the-guild.dev/graphql/codegen/docs/config-reference/codegen-config
- プラグイン： https://the-guild.dev/graphql/codegen/plugins/typescript/typescript

## インストール＆セットアップ

```
npm install graphql
npm install -D typescript
npm install -D @graphql-codegen/cli
```

セットアップ（全てデフォルト指定）
```
$ npx graphql-code-generator init

    Welcome to GraphQL Code Generator!
    Answer few questions and we will setup everything for you.

? What type of application are you building? Application built with React
? Where is your schema?: (path or url) http://localhost:4000
? Where are your operations and fragments?: src/**/*.tsx
? Where to write the output: src/gql
? Do you want to generate an introspection file? Yes
? How to name the config file? codegen.ts
? What script in package.json should run the codegen? codegen
Fetching latest versions of selected plugins...
(node:224940) ExperimentalWarning: buffer.Blob is an experimental feature. This feature could change at any time
(Use `node --trace-warnings ...` to show where the warning was created)

    Config file generated at codegen.ts

      $ npm install

    To install the plugins.

      $ npm run codegen

    To run GraphQL Code Generator.
```

生成されたcodegen.ts
```ts
import type { CodegenConfig } from '@graphql-codegen/cli';

const config: CodegenConfig = {
  overwrite: true,
  schema: "http://localhost:4000",
  documents: "src/**/*.tsx",
  generates: {
    "src/gql": {
      preset: "client",
      plugins: []
    },
    "./graphql.schema.json": {
      plugins: ["introspection"]
    }
  }
};

export default config;
```


## 設定ファイル

https://the-guild.dev/graphql/codegen/docs/config-reference/codegen-config

設定ファイル生成
```
npx graphql-code-generator --config ./path/to/codegen.ts
```

例（複数指定）：
```ts
import { CodegenConfig } from '@graphql-codegen/cli';

const config: CodegenConfig = {
  // documents: ['src/**/*.tsx', '!src/gql/**/*'],
  generates: {
    './src/gql/github/': {
      schema: 'https://docs.github.com/public/schema.docs.graphql',
      preset: 'client',
      plugins: []
    },
    './src/gql/gitlab/': {
      schema: 'https://gitlab.com/api/graphql?remove_deprecated=true',
      preset: 'client',
      plugins: []
    }
  },
  overwrite: true
};

export default config;
```

### documents (GraphQL Document とは)

gql を定義しているファイルを指定する。指定することでQueryやMutations等の型が生成される。※ 名前付け必須。なければ自動生成対象にならない

GraphQL Document：
- GraphQL へ投げる query やら mutation やらの string のことを指す
- 具体的には、 gql に渡している部分　
```
const GET_GREETING = gql`
    query GetGreeting($language: String!) {
        greeting(language: $language) {
            message 
        }
    }
`;
```

ref: https://zenn.dev/link/comments/600791d07ec1a1

### namingConvention

出力の命名規則をオーバーライドできる
- デフォルト：change-case-all#pascalCase
- lower、upper、camel、pascal 等変更できる
- 範囲指定：typeNames、enumValues
- "keep"を使用してすべてのGraphQL名をそのまま保持することもできる(例：`enumValues: 'keep'`)
- アンダースコアを保持する場合は、transformUnderscoreをtrueに設定

```ts
import { CodegenConfig } from '@graphql-codegen/cli'
 
const config: CodegenConfig = {
  // ...
  config: {
    namingConvention: {
      typeNames: 'change-case-all#pascalCase',
      enumValues: 'change-case-all#upperCase'
    }
  }
  // ...
}
export default config
```

ref： https://the-guild.dev/graphql/codegen/docs/config-reference/naming-convention

## Lifecycle Hooks

codegenで、特定のイベントで実行できるスクリプトを指定可能

```ts
import { CodegenConfig } from '@graphql-codegen/cli'
 
const config: CodegenConfig = {
  schema: 'http://localhost:4000/graphql',
  documents: ['src/**/*.tsx'],
  generates: {
    './src/gql/': {
      preset: 'client'
    }
  },
  // 
  hooks: {
    afterOneFileWrite: ['prettier --write']
  }
}
 
export default config
```

hooks一覧：
- afterStart：
	- codegenの開始時に引数なしでトリガー (codegen.tsがロードされた後)。
- onWatchTriggered： 
	- ウォッチモード使用時に、ファイルが変更されるたびにトリガー。イベントのタイプ（例：changed）とファイルのパスの2つの引数でトリガー。
- onError：
	- codegenに一般的なエラーが発生した場合にトリガー。引数はエラーを含む文字列である。
- beforeAllFileWrite：
	- codegenが出力を作成した後、ファイルをファイルシステムに書き込む前に実行。  複数の引数（関連するすべてのファイルのパス）でトリガーする 。
- beforeOneFileWrite：
	- ファイルがファイルシステムに書き込まれる前にトリガー。ファイルのパスで実行される。  前回実行時からファイルの内容が変化していない場合、このフックは発動しない。
- afterOneFileWrite：
	- ファイルがファイルシステムに書き込まれた後にトリガー。ファイルのパスを指定して実行される。ファイルの内容が前回の実行から変更されていない場合、このフックはトリガーされない。
- afterAllFileWrite：
	- すべてのファイルをファイルシステムに書き込んだ後に実行されます。複数の引数 (すべてのファイルのパス) でトリガー。
- beforeDone：
	- 引数なしで、codegenが閉じる直前、またはウォッチモードが停止したときにトリー。

出力先単位で指定できるのは以下のみ。他はルートのみ指定可能。
- `beforeOneFileWrite`
- `afterOneFileWrite`

## プラグイン

- [typescript](https://the-guild.dev/graphql/codegen/plugins/typescript/typescript) ：GraphQL スキーマに基づいて基本 TypeScript タイプを生成
- @graphql-codegen/typescript-operations：
	- GraphQL SchemaとGraphQLの操作とフラグメントに基づいてTypeScript型を生成。
	- GraphQLドキュメントの型を生成：Query、Mutation、Subscription、Fragment
- @graphql-codegen/typescript-urql : urql 用。 ref: [urqlをさわってみるぞ](https://zenn.dev/monicle/articles/e427d17935d019)

※ apollo の場合は以下っぽい
```
plugins: 
- "typescript" 
- "typescript-operations" 
- "typescript-react-apollo"
```

## コード自動生成

- GraphQLスキーマからコード自動生成：
	- [GraphQL Code Generator ドキュメント](https://the-guild.dev/graphql/codegen)
	- [Github](https://github.com/dotansimha/graphql-code-generator/)

モック データ生成プラグイン：
- [graphql-codegen-typescript-mock-data](https://github.com/ardeois/graphql-codegen-typescript-mock-data)
- refs
	- [モックを自動生成してくれるgraphql-codegen-typescript-mock-dataが便利だった](https://zenn.dev/vallis/articles/83b1a10d5325e9)
	- [msw + graphql-codegen-typescript-mock-data + testing-library/react-nativeでインテグレーションテストを実装する](https://www.wheatandcat.me/entry/2022/09/01/223853)
	- [ジョインしたのと同時期に導入されて感動した MSW とその周辺パッケージ についてのお話](https://kakehashi-dev.hatenablog.com/entry/2022/12/13/000000)

※GithubとGitlabのスキーマから自動生成サンプル
```typescript
import { CodegenConfig } from '@graphql-codegen/cli';

const config: CodegenConfig = {
  generates: {
    './src/gql/github/': {
      schema: 'https://docs.github.com/public/schema.docs.graphql',
      documents: 'src/data/github/github-queries.ts',
      preset: 'client',
      plugins: [] // 'typescript'を入れると型が２重出力される
    },
    './src/gql/gitlab/': {
      schema: 'https://gitlab.com/api/graphql?remove_deprecated=true',
      documents: 'src/data/gitlab/gitlab-queries.ts',
      preset: 'client',
      plugins: [] // 'typescript'を入れると型が２重出力される
    },
    './src/mocks/github/mock.ts': { 
      schema: 'https://docs.github.com/public/schema.docs.graphql',
      plugins: [
        {
          'typescript-mock-data': {
            typesFile: 'src/gql/github/graphql.ts',
            prefix: 'mockGithub',
            terminateCircularRelationships: true // 循環関係による無限再帰防止
          }
        }
      ]
    },
    './src/mocks/gitlab/mock.ts': {
      schema: 'https://gitlab.com/api/graphql?remove_deprecated=true',
      plugins: [
        {
          'typescript-mock-data': {
            typesFile: 'src/gql/gitlab/graphql.ts',
            prefix: 'mockGitlab',
            terminateCircularRelationships: true // 循環関係による無限再帰防止
          }
        }
      ]
    }
  },
  overwrite: true
};

export default config;
```

gql
```typescript
export const GET_GITHUB_REPOSITORIES = gql`
  query GetGithubRepositories($owner: String!) {
    user(login: $owner) {
      repositories(last: 10) {
        nodes {
          name
          url
        }
      }
    }
  }
`;
```

msw の handlers.ts
```typescript
// path: src/mocks
import { graphql } from 'msw';
// GET_GITHUB_REPOSITORIES から 生成された type
import { GetGithubRepositoriesDocument } from 'src/gql/github/graphql';
// User モックデータ生成関数
import { mockGithubUser } from './github/mock';

export const handlers = [
  graphql.query(GetGithubRepositoriesDocument, (req, res, ctx) => {
    return res(
      ctx.data({
        // 引数で値の上書き、返す関連データの指定が可能
        user: mockGithubUser()
      })
    );
  })
];
```


#GraphQL
#blog 
