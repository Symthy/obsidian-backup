
設計する上で、公式ドキュメント（英語）を翻訳機で訳してまとめただけ。

## ファイル & パッケージ構成

- 全てのファイルにパッケージを定義
- 同じパッケージのファイルは全てパッケージ名と一致する同じディレクトリに入れる
- パッケージの最後のコンポーネントは バージョンにする
- パッケージ名の形式：lower_snake_case
- ファイル名の形式：lower_snake_case.proto
- 繰り返しフィールドに複数形の名前を使用

```sh
.
└── proto
    ├── buf.yaml
    └── foo
        └── bar
            ├── bat
            │   └── v1
            │       └── bat.proto // package foo.bar.bat.v1
            └── baz
                └── v1
                    ├── baz.proto         // package foo.bar.baz.v1
                    └── baz_service.proto // package foo.bar.baz.v1
```

- 同じパッケージ内の全protoファイルで、以下オプションは揃える（値と有無）
	-  `csharp_namespace`
	-  `go_package`
	-  `java_multiple_files`
	-  `java_package`
	-  `php_namespace`
	-  `ruby_package`
	-  `swift_prefix`


```protobuf
// foo_one.proto
syntax = "proto3";

package foo.v1;

option go_package = "foov1";
option java_multiple_files = true;
option java_package = "com.foo.v1";
```

```protobuf
// foo_two.proto
syntax = "proto3";

package foo.v1;

option go_package = "foov1";
option java_multiple_files = true;
option java_package = "com.foo.v1";
```

## Message

- Message名はPascalCase
- フィールド名は lower_snake_case

```proto
syntax = "proto3";

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;

  oneof test_oneof {  // セットで扱うものをまとめられる
    string name = 4;
    SubMessage sub_message = 5;
  }
  optional string song_name = 6;
  repeated string keys = 7;
  map<string, Project> projects = 8; // map に repeated は使えない
  reserved 2, 15, 9 to 11;  // reserved: 予約済みフィールド
  reserved "foo", "bar";    // 主に削除済みのフィールドに使用
}

message Project {
  ...
}
```

### Field

種類：
- oneof
- optional
- repeated
- map
- reserved

スカラー値の型：
- double/float
- int32/int64
- uint32/uint64：正の値
- sint32/sint64：int32/int64より効率的に負の数をエンコードできる
- fixed32：常に4bytes。2^28を超える場合は uint32より効率的にエンコード
- fixed64：常に8bytes。2^56を超える場合は uint64より効率的にエンコード
- sfixed32：常に4bytes。
- sfixed64：常に8bytes。
- bool
- string
- bytes： 2^32以下の任意のバイトシーケンス

ルール：
- 1 ～ 15 のフィールド番号は、（1byteのため）非常に頻繁に使用するフィールド用に予約する
- たまにしか使わないフィールドは、16～2047 (2byte) を使い、頻繁に使用するフィールドとして予約できる余地を残しておく必要がる
- フィールド番号の最大は 2^29 - 1 (= 536,870,911)
- 19000から19999までの数字は ProtocolBuffers の実装のために予約されているため使用不可（使用した場合はコンパイラから警告）

### Nested Type

できるが、ネストされたMessageの使用は避ける

```proto
message SearchResponse {
  message Result {
    string url = 1;
    string title = 2;
    repeated string snippets = 3;
  }
  repeated Result results = 1;
}
```

他のメッセージで利用する場合
```proto
message SomeOtherMessage {
  SearchResponse.Result result = 1;
}
```

### Any

- google/protobuf/any.protoをインポートする必要がある
- Anyには、任意のシリアル化されたメッセージがバイトとして含まれ、そのメッセージのタイプのグローバル一意識別子として機能し、解決されるURLが含まれる
- 言語の実装で、Any値を型安全な方法でパックおよびアンパックするランタイムライブラリヘルパーがサポート

```proto
import "google/protobuf/any.proto";

message ErrorStatus {
  string message = 1;
  repeated google.protobuf.Any details = 2;
}
```


## Enum

- Enumにはallow_aliasオプションを設定しない
- Enum名はPascalCase
- Enum値の名前はUPPER_SNAKE_CASE
- Enum値の名前の先頭には、Enum名のUPPER_SNAKE_CASEを付ける
	- Enum：FooBar の場合、 Enum値の名前：FOO_BAR_～
- すべての Enum の 0 の値の末尾には_UNSPECIFIEDを付ける

```proto
enum Corpus {
  CORPUS_UNSPECIFIED = 0;
  CORPUS_UNIVERSAL = 1;
  CORPUS_WEB = 2;
  CORPUS_IMAGES = 3;
  CORPUS_LOCAL = 4;
  CORPUS_NEWS = 5;
  CORPUS_PRODUCTS = 6;
  CORPUS_VIDEO = 7;
}

message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
  Corpus corpus = 4;
}
```

allow_alias：同じフィールド番号を許容
```proto
enum EnumAllowingAlias {
  option allow_alias = true;
  EAA_UNSPECIFIED = 0;
  EAA_STARTED = 1;
  EAA_RUNNING = 1;
  EAA_FINISHED = 2;
}

enum EnumNotAllowingAlias {
  ENAA_UNSPECIFIED = 0;
  ENAA_STARTED = 1;
  // ENAA_RUNNING = 1;  // コメントを外すと警告メッセージ表示
  ENAA_FINISHED = 2;
}
```

※列挙子の数に言語固有の制限がある場合がある (1つの言語で1000以下)

## Service

- Service名はPascalCase
- Service名の末尾にはServiceを付ける
- RPC名はPascalCaseにする
- すべてのRPC要求および応答メッセージは、Protobufスキーマ全体で一意である必要がある
- すべてのRPC要求および応答メッセージには、以下の命名にする
	- ＜MethodName＞Request
	- ＜MethodName＞Response
	- ＜ServiceName＞＜MethodName＞Request
	- ＜ServiceName＞＜MethodName＞Response

```proto
service FooService {
  rpc GetSomething(GetSomethingRequest) returns (GetSomethingResponse);
  rpc ListSomething(ListSomethingRequest) returns (ListSomethingResponse);
}
```

※（議論の余地はあるが）RPCのストリーミングを避けることを推奨する。（これらは確かに非常に価値のある特定のユースケースを持っていますが、全体としては多くの問題を引き起こし、RPCフレームワークのロジックをスタックに押し上げるため、通常はより信頼性の高いアーキテクチャの開発を妨げる）

## Proto ベストプラクティス

- タグ番号は再利用しない
- フィールドのタイプは変更しない
- 必須フィールドを追加しない
	- 代わりに `// required` を追加して、APIコントラクトを文書化する
	- ※ proto3 から完全に削除された
- フィールドが多い(数百)メッセージは作らない
- Enumに 宣言の最初の値としてデフォルトの Unspecified Value（XXX_UNSPECIFIED）を含める
	- デフォルト値が存在しない場合は最初に宣言された値が返されるため
	- enumは最初の値がで 0 あることを要求するため値は 0 にする
- Well-Known Types と Common Types を使う
	- 以下の共通型、共有型を埋め込むことを強く推奨
		- Well-Known Types
			- [duration](https://github.com/protocolbuffers/protobuf/blob/main/src/google/protobuf/duration.proto)：符号付き固定長の時間スパン (例：42s)
			- [timestamp](https://github.com/protocolbuffers/protobuf/blob/main/src/google/protobuf/timestamp.proto)：例 2017-01-15T01:30:15.01Z
			- [field_mask](https://github.com/protocolbuffers/protobuf/blob/main/src/google/protobuf/field_mask.proto)：指定のフィールドのみ取るためのもの
		- Common Types
			- [interval](https://github.com/googleapis/googleapis/blob/master/google/type/interval.proto)： 時間間隔（例：2017-01-15T01:30:15.01Z - 2017-01-16T02:30:15.01Z）
			- [date](https://github.com/googleapis/googleapis/blob/master/google/type/date.proto)： 例 2005-09-19
			- [dayofweek](https://github.com/googleapis/googleapis/blob/master/google/type/dayofweek.proto)： 例 Monday
			- [timeofday](https://github.com/googleapis/googleapis/blob/master/google/type/timeofday.proto)： 例 10:42:23
			- [latlng](https://github.com/googleapis/googleapis/blob/master/google/type/latlng.proto)： 緯度/経度 の組 （例：37.386051 latitude and -122.083855 longitude）
			- [money](https://github.com/googleapis/googleapis/blob/master/google/type/money.proto)： 例 42 USD
			- [postal_address](https://github.com/googleapis/googleapis/blob/master/google/type/postal_address.proto)：郵便の住所
			- [color](https://github.com/googleapis/googleapis/blob/master/google/type/color.proto)：RGBAカラースペース
			- [month](https://github.com/googleapis/googleapis/blob/master/google/type/month.proto) ：例 April
	- 完全に適した共通型がすでに存在する場合、コード内で `int32 timestamp_seconds_since_epoch` や `int64 timeout_millis` を使用しない
-  広く使用されることを期待するMessage Type や Enum は別のファイルに定義し、誰でも簡単に使えるようにする
- 削除されたフィールドのタグ番号は予約（reserved）して、後で誤って再使用しないようにする
- フィールドのデフォルト値は変更しない（proto3ではデフォルト値を設定する機能は削除）
- rotated から Scalar に移行しない。クラッシュはしないがデータ失われる（逆は大丈夫）
- 生成されたコードのスタイルガイドに従う
- テキスト形式のメッセージをやり取りに使用しない（テキスト形式は、人による編集とデバッグにのみ使用）
- ビルド間でのシリアライゼーションの安定性に決して依存しない
	- protoシリアライズの安定性は、バイナリ間でも、同じバイナリのビルド間でも保証されない（ビルドする度中身が変わる）ため、キャッシュキーの構築等で依存してはいけない

ref: https://protobuf.dev/programming-guides/dos-donts/

## API ベストプラクティス

公式ドキュメントの記載は、長期的でバグのない進化を優先するためにトレードオフを行った上での提案
ref: https://protobuf.dev/programming-guides/api/

### フィールドとメッセージを正確かつ簡潔に文書化する

- 各フィールドの制約、期待、解釈をできるだけ短い言葉で文書化
- 時間経過とともに長くなるかもしれないが、全体的に見て簡潔にすることを目指す

### Wire (≒API?)と Storage でメッセージを使い分ける

 - クライアントに公開するトップレベルの proto が、ディスクに保存する proto を同じにしない
	- クライアントに影響を与えることなく、保存形式を変更できる自由度が必要
	- コードをレイヤー化し、モジュールが client protos、storage protos、translation のいずれかを処理するように分ける
- 例外
	- protoフィールドがgoogle.typeやgoogle.protobufのような一般的な型であれば、その型をストレージとAPIの両方として使用可
	- 極めてパフォーマンスに敏感であれば、柔軟性を実行速度と引き換えにする価値があるかもしれない（ミリ秒単位のレイテンシーで数百万QPSを実現？）
	- 以下が全て当てはまる
		- サービスがストレージシステム
		- クライアントの構造化データに基づいた意思決定ができない
		- システムが、単に保存、ロード、クライアントのリクエストに応じたクエリーを提供する場合

### Mutations (更新API) の場合、データの完全なリプレイスではなく、部分的な更新または追加のみの更新をサポートする

-  (1) Update Field-mask を使用する。
	- クライアントが変更するフィールドを渡し、それらのフィールドだけを更新要求に含める。
	- サーバは他のフィールドをそのままにし、マスクで指定されたフィールドのみを更新する。
	- 一般的に、マスクの構造は応答 proto の構造を反映する必要がある（＝ FooにBarが含まれている場合、FooMaskにはBarMaskを含める）
- (2) 個々のピースを変化させる より狭い Mutations API を公開する
	- 例：UpdateEmployeeRequestの代わりに、PromoteEmployeeRequest、SetEmployeePayRequest、TransferEmployeeRequestなど（用途特化？）
	- カスタム更新方法は、非常に柔軟な更新方法よりも監視、監査、およびセキュリティが容易かつ実装や呼び出しも簡単。ただし、その数が多いとAPIの認知負荷が増大する可能性があるため注意。

### トップレベルのリクエストまたはレスポンスの proto にプリミティブ型（基本のデータ型）を含めない

- トップレベルの proto は、ほとんどの場合、独立して成長できる他のメッセージのコンテナである必要がある
- 必要なプリミティブ型が1つだけの場合でも、メッセージにラップすることで、その型を拡張し、類似した値を返す他のメソッド間で型を共有するための明確なパスが得られる

```proto
message MultiplicationResponse {
  // 悪い例：複素数を返す必要があり、同じ複数フィールド型を返すレスポンスが必要な場合に
  // どちらにもプリミティブ型のフィールドを追加するのは避けたいはず
  optional double result;

  // 良い例：他のメソッドはこのタイプを共有でき、成長可能
  // サービスは、新しい機能 (単位、信頼区間など) を容易に追加可能。
  optional NumericResult result;
}

message NumericResult {
  optional double real_value;
  optional double complex_value;
  optional UnitType units;
}
```

トップレベルプリミティブの例外
- プロトをエンコードするが、サーバー上でのみ構築および解析される不透明な文字列 (またはバイト) 
- 継続トークン、バージョン情報トークン、IDは、文字列が実際に構造化プロトのエンコードである場合、すべて文字列として返すことができる。

### （現在は2つの状態を持つが）後でさらに多くの状態を持つ可能性があるものにはbooleanを使用しない

フィールドにbooleanを使用する場合は、フィールドが（将来的にも）実際に2つの可能な状態だけになるかを確認する。


```proto
message GooglePlusPost {
  // 悪い例：この投稿を2つのカラムにまたがってレンダリングするかどうか
  optional bool big_post;

  // 良い例：この投稿を表示するクライアントのためのレンダリングヒント
  // クライアントは、この投稿をどの程度目立つように表示するかを決定するために、これを使用。
  // ない場合は、デフォルトのレンダリングを想定。
  optional LayoutConfig layout_config;
}

message Photo {
  // 悪い例: GIFかどうか
  optional bool gif;

  // 良い例: 写真のファイル形式（例：GIF、WebP、PNG）。
  optional PhotoType type;
}
```

概念を混同する enum に state を追加することには、注意が必要
- もし state が enum に新しい次元を導入する場合や、複数のアプリケーションの動作を意味する場合、ほぼ間違いなく別のフィールドが必要

### IDに整数フィールドをほとんど使用しない

- オブジェクトの識別子としてint 64 を使いたくなるが、文字列を選択する
- 必要に応じてIDスペースを変更し、衝突の可能性を減らせる（2^64は今では大きくはない）
- 構造化識別子を文字列としてエンコードすることで、クライアントに不透明なBlobとして扱うように促すことも可能。
	- この場合、文字列の裏にprotoが必要だが、プロトを文字列フィールドにシリアライズすることができる（Web-safe Base64としてエンコード）。
	- これにより、クライアントが公開するAPIから内部の詳細を取り除くことができる。（その場合は 「Web-Safe Encoding Binary Proto Serializationで不透明なデータを文字列でエンコードする」 を参照）

```proto
message GetFooRequest {
  // Which Foo to fetch.
  optional string foo_id;
}

// websafe-base64-encoded & シリアライして、 GetFooRequest.foo_idフィールドにセット
message InternalFooRef {
  // Only one of these two is set. Foos that have already been
  // migrated use the spanner_foo_id and Foos still living in
  // Caribou Storage Server have a classic_foo_id.
  optional bytes spanner_foo_id;
  optional int64 classic_foo_id;
}
```

### Web-Safe Encoding Binary Proto Serializationで不透明なデータを文字列でエンコードする

- クライアントから見えるフィールドの不透明なデータ(継続トークン、シリアル化されたID、バージョン情報など)をエンコードする場合は、クライアントが不透明なBlobとして扱う必要があることを文書化する。
- これらのフィールドには、必ずバイナリ形式のプロトシリアライゼーションを使用し、テキスト形式や独自の工夫をしない。不透明なフィールドにエンコードされたデータを拡張する必要がある場合、まだ使用していなければ、プロトコルバッファのシリアル化を再発明することになるため。
- 不透明なフィールドに入るフィールドを保持する内部protoを定義し (フィールドが1つだけ必要な場合でも) 、この内部protoをバイトにシリアル化し、その結果をweb-safe base-64で文字列フィールドにエンコードします。
- プロトシリアライゼーションを使用するまれな例外：非常に時折、慎重に構築された代替形式からのコンパクトさの勝利は価値がある。


### クライアントが構築または解析すると予想される文字列内のデータをエンコードしない

ネットワーク上では効率が悪く、proto の利用者にとっては手間がかかり、ドキュメントを読んでいる人にとっては混乱を招く。

クライアントはエンコーディングについても気にする必要がある
- リストはコンマ区切りか?
- この信頼できないデータは正しくエスケープされているか?
- 数字は10を底としているか?


クライアントに実際のメッセージやプリミティブタイプを送信させる方がよい。その方が、ネットワーク全体でよりコンパクトになり、クライアントにとってより明確になる
- サービスで複数言語のクライアントを取得した場合に特に悪化する。各自が適切なパーサーやビルダーを選択しなければならなくなり、最悪の場合それを書かなければならなくなる。
- より一般的には、正しいプリミティブ型を選択する。
	- 『Protocol Buffer Language Guide』の 「Scalar Value Types」 の表を参照

#### フロントエンド proto でHTMLを返す

**JavaScriptクライアントには、APIのフィールドでHTMLやJSONを返さない。**
（返すとAPIを特定のUIに結びつけるため良くない）

具体的な3つの危険性：
- 「スクラッピー」 な非Webクライアントは、HTMLやJSONを解析して、フォーマットを変更した場合の脆弱性や、解析が悪い場合の脆弱性につながるデータを取得することになる
- Webクライアントは、そのHTMLがサニタイズされずに返された場合、XSSエクスプロイトに対して脆弱になる。
- 返されるタグとクラスは、特定のスタイルシートとDOM構造を想定しているが、リリースごとに構造が変化し、JavaScriptクライアントがサーバーより古くなると、サーバーが返すHTMLが古いクライアントで正しくレンダリングされなくなるバージョンスキューの問題が発生するリスクがある。（リリース頻度の高いプロジェクトでは、これはエッジケースではない）

最初のページロード以外では、通常データを返しクライアント側のテンプレートを使用してクライアント上でHTMLを構築する方がよい

### クライアントが使用できない可能性のあるフィールドを含めない

クライアントに公開するAPIには、
- システムとの対話方法を記述するためだけのもののみ含める
- その中に他の何かを含めると、それを理解しようとする人に認知的オーバーヘッドが加わる

以前は応答プロトコルでデバッグデータを返すのが一般的でしたが、今はより良い方法がある
- RPCレスポンス拡張（「サイドチャネル」とも呼ばれる）により、あるプロトでクライアントインタフェースを記述し、別のプロトでデバッグサーフェスを記述することができる
- （同様に応答 proto で実験名を返すのは、以前はログ記録の利便性があり、不文律の契約では、クライアントは後続のアクションでそれらの実験を送り返していた）同じことを実現する方法は、分析パイプラインでログの結合を行うこと。

１つの例外:
- 継続的なリアルタイム分析が必要で、マシンの予算が少ない場合は、ログ結合の実行は困難な場合がある。
- コストが決定要因である場合は、ログデータを事前に非正規化することが有効な場合がある。
- ログデータのラウンドトリップが必要な場合は、不透明なBlobとしてクライアントに送信し、要求フィールドと応答フィールドを文書化する。

Caution:
- 要求のたびに隠しデータを返したり往復したりするのは、サービスを利用するための本当のコストを隠していることになり、良くない。

### 継続トークンなしでページ区切りAPIを定義しない

```proto
message FooQuery {
  // Bad:最初のクエリと2番目のクエリの間でデータが変更された場合、
  // これらの各戦略によって結果を見逃す可能性があります。
  // 最終的に一貫性のある世界(つまり、Bigtableにバックアップされたストレージ)では、
  // 新しいデータの後に古いデータが表示されることは珍しくない。
  // また、オフセットベースとページベースのアプローチはすべてソート順を前提としているため、
  // ある程度の柔軟性が失われる。
  optional int64 max_timestamp_ms;
  optional int32 result_offset;
  optional int32 page_number;
  optional int32 page_size;

  // Good: 柔軟性がある。これを FooQueryResponse で返し、
  // クライアントが次のクエリでそれを返すようにする。
  optional string next_page_token;
}
```


ページ分割APIのベストプラクティス
- シリアル化する内部プロトに裏打ちされた不透明な継続トークン (next_page_token) を使用してから、WebSafeBase64Escape (C++) またはBaseEncoding.base64Url().encode (Java) を使用すること。
- その内部プロトは多くの分野を含む可能性がある。重要なのは、それが柔軟性をもたらし、選択すれば、クライアントに安定した結果をもたらすということ。

```proto
message InternalPaginationToken {
  // これまでに確認されたIDを追跡する
  // これにより、継続トークンが大きくなる代わりに、完璧な想起が可能になる
  // --特に、ユーザがページを戻したとき
  repeated FooRef seen_ids;

  // seen_idsストラテジーに似ているが、seen_idsをBloomフィルタにかけることで
  // バイトを節約し、精度を犠牲にする
  optional bytes bloom_filter;

  // 合理的な最初のカットであり、より長く機能する可能性がある. 
  // 継続トークンに埋め込んでおけば、後でクライアントに影響を与えることなく変更できる
  optional int64 max_timestamp_ms;
}
```

### 関連するフィールドを新しいメッセージにまとめる。親和性の高いフィールドだけをネストする

```proto
message Foo {
  // Bad: 
  optional int price;
  optional CurrencyType currency;

  // Better: Fooの価格と通貨をカプセル化
  optional CurrencyAmount price;
}
```


後で関連するフィールドを持つ可能性がある場合、これを回避するために事前に入れる。
```proto
message Foo {
  // DEPRECATED! Use currency_amount.
  optional int price [deprecated = true];

  // The price and currency of this Foo.
  optional google.type.Money currency_amount;
}
```

疎結合はシステムを開発する際のベストプラクティスだが、.protoファイルを設計する際には必ずしもそのプラクティスが適用されない場合がある。
```proto
message Photo {
  // Bad: PhotoMetadataはPhotoの範囲外で再利用される可能性が高いので、
  // 入れ子にせず、アクセスしやすくしておくといいかもしれませんね。
  message PhotoMetadata {
    optional int32 width = 1;
    optional int32 height = 2;
  }
  optional PhotoMetadata metadata = 1;
}

message FooConfiguration {
  // Good: FooConfiguration.RuleをFooConfigurationのスコープ外で再利用すると、
  // 無関係なコンポーネントと密接に結合する可能性が高いため、ネスティングして再利用を防ぐ。
  message Rule {
    optional float multiplier = 1;
  }
  repeated Rule rules = 1;
}
```

### 読み取り要求にフィールド読み取りマスクを含める

読み取りマスク
- クライアント側に明確な期待値を設定し、返すデータの量を制御し、バックエンドがクライアントが必要とするデータのみを取得できるようにする。
- すべてのフィールドがtrueに設定された暗黙の読み取りマスクがあるかのように要求を処理する（protoが大きくなるにつれてコストが大きくなる）
- 暗黙的な (宣言されていない) 読み取りマスク は Bad

これは、プロトコールが大きくなるにつれてコストがかかる可能性があります。

```proto
// Recommended: use google.protobuf.FieldMask

// Alternative one:
message FooReadMask {
  optional bool return_field1;
  optional bool return_field2;
}

// Alternative two:
message BarReadMask {
  // 返すフィールドのタグ番号。
  repeated int32 fields_to_return;
}
```

### 一貫した読み取りを可能にするバージョンフィールドを含める

（分散システムの話？）

クライアントが書き込みを行った後に同じオブジェクトを読み込む場合、書き込んだ内容が戻ってくることを期待するが、その期待値が基となるストレージシステムにとって妥当でない場合もある。  
  
サーバーはローカルの値を読み、ローカルの version_info が、期待される version_info より小さい場合、リモートレプリカから読み取って最新の値を見つける。

version_info は文字列としてエンコードされたプロトで、変異が起こったデータセンターとコミットされたタイムスタンプを含む。（エンコードは「Web-Safe Encoding Binary Proto Serializationで不透明なデータを文字列でエンコードする」参照）

### 同じデータ型を返すRPCには一貫したリクエストオプションを使用する

要求オプションを保持する単一の個別のメッセージを作成し、それを最上位の各要求メッセージに含める。

```proto
message FooRequestOptions {
  // フィールドレベル読み取りマスク。要求されたフィールドのみ返す。
  // クライアントは（バックエンドが要求を最適化するために）必要なフィールドのみ要求
  optional FooReadMask read_mask;

  // 最大でこの数のコメントが返す。
  // スパムとしてマークされたコメントは、最大コメント数にカウントされない。
  // デフォルトでは、コメントは返されない。
  optional int max_comments_to_return;

  // このサポートされている型リストにない埋め込みを含むfooは、このリストで指定された埋め込みにダウンコンバートされた埋め込みを持つ。サポートされるタイプリストが指定されない場合、埋め込みは返されない。埋め込みをるサポートされている型のいずれかにダウンコンバートできない場合、埋め込みは返されない。クライアントは、EmbedTypes.protoから少なくともTHING_V2埋め込み型を常に含めることを強くお勧めします。
  repeated EmbedType embed_supported_types_list;
}

message GetFooRequest {
  // ビューアーがFooにアクセスできない場合、またはFooが削除されている場合、
  // 応答は空になるが成功する。
  optional string foo_id;

  // クライアントはこのフィールドを含める必要がある
  // FooRequestOptions が空のままだと、サーバの返却は INVALID_ARGUMENT になる
  optional FooRequestOptions params;
}

message ListFooRequest {
  // 検索では100%が再現されるが、より多くの句がパフォーマンスに影響する。
  optional FooQuery query;

  // クライアントはこのフィールドを含める必要がある
  // FooRequestOptionsが空のままだと、サーバーはINVALID_ARGUMENTを返します。
  optional FooRequestOptions params;
}
```

### Batch/Multi-phase リクエスト

- 可能な限り、Mutations を 原始的なものにする。さらに重要なのは、Mutations を冪等性にすること。部分的な失敗の完全な再試行は、データを破損したり複製したりしてはならない。  
- 時に、パフォーマンス上の理由から、複数の操作をカプセル化した単一のRPCが必要になることがある。部分的な失敗の場合（あるものが成功し、あるものが失敗した場合）クライアントに知らせるのが一番。  
- RPCをfailedに設定し、成功と失敗の両方の詳細をRPC status protoで返すことを検討する。  

### 小さなデータを返す/操作するメソッドを作成し、クライアントが複数の要求をまとめてUIを構成することを期待する

1回のラウンドトリップで多くの厳密に指定されたデータのビット？を照会できるため、クライアントが必要なものを構成することで、サーバーを変更せずに幅広いUXオプションを提供することができる 
  
これは、フロントエンドや middle-tier サーバーに最も関係がある。（多くのサービスが独自のバッチングAPIを公開している）

### モバイルやウェブで連続したラウンドトリップが必要な場合、1回限りのRPCを作成する

一般的な進化は、1つの繰り返しフィールドが複数の関連した繰り返しフィールドになる必要があることである。つまり、並列の繰り返しフィールドを作成するか、値を保持する新しいメッセージで新しい繰り返しフィールドを定義し、クライアントをそのフィールドに移行するかである。
  
繰り返しのメッセージから始めると、進化は些細なことになる

```proto
// 写真に適用される補正の種類
enum EnhancementType {
  ENHANCEMENT_TYPE_UNSPECIFIED;
  RED_EYE_REDUCTION;
  SKIN_SOFTENING;
}

message PhotoEnhancement {
  optional EnhancementType type;
}

message PhotoEnhancementReply {
  // Good: PhotoEnhancementは、enumだけでなく、
  // より多くのフィールドを必要とする拡張機能を記述して成長可能
  repeated PhotoEnhancement enhancements;

  // Bad: もし、エンハンスメントに関連するパラメータを返したい場合は、
  // 並列配列を導入するか（酷い）、このフィールドを非推奨とし、繰り返しメッセージを導入する必要がある。
  repeated EnhancementType enhancement_types;
}
```

例外：
- レイテンシが重要なアプリケーションでは、プリミティブ型の並列配列の方がメッセージの単一配列よりも構築と削除が高速である
- また `[packed=true]` (フィールドタグを除外する) を使用する場合は、ネットワーク上で小さくすることもできる。固定数の配列を割り当てる方が、N個のメッセージを割り当てるよりも手間がかからない。※Proto 3ではpackingは自動;明示的に指定する必要はありません。

### Proto Maps を使用する

- proto3からは `Map<scalar, **message**>` を使用する
- 事前に構造がわからない任意のデータを表す場合は、google.protobuf.Any を使用

### 冪等性を優先

- クライアントが再試行ロジックを持つ場合がある。
- 再試行が Mutations である場合、データ重複などに繋がる。
- 重複書き込みを避ける簡単な方法は、クライアントが作成したリクエストIDを指定できるようにすることで、サーバーがそれを元に重複排除すること（例えば、コンテンツのハッシュやUUIDなど）。

### サービス名はグローバル(ネットワーク上)でユニークなものにする

- サービス名（つまり、.protoファイルのserviceキーワードの後の部分）は、サービスクラス名を生成するためだけでなく、意外と多くの場所で使われる。そのため、この名前は重要。  
- 厄介なのは、これらのツールは、サービス名がネットワーク上で一意であるという暗黙の前提を置いていること。さらに悪いことに、これらのツールが使用するサービス名は、修飾されたサービス名（例：my_package.MyService）ではなく、修飾されていないサービス名（例：MyService）です。  
- このため、たとえ特定のパッケージ内で定義されたサービスであっても、サービス名の命名衝突を防ぐための措置を講じることが理にかなっている。例えば、Watcherという名前のサービスは問題を起こす可能性が高いため、MyProjectWatcherのようなものが良い。

### すべてのRPCで（許可制の）期限を指定して強制する

- デフォルトでは、RPC はタイムアウトを持たない。リクエストは完了時にのみ解放される。
- バックエンドリソースを占有するかもしれないため、すべてのリクエストが終了できるようにデフォルトの期限を設定することは良い防御方法。（過去には、これを実施しなかったために、主要なサービスにおいて深刻な問題が発生したこともある）
- RPCクライアントは、発信するRPCに期限を設定すべきであり、標準的なフレームワークを使用する場合は、通常デフォルトで設定される。
- デッドラインは、リクエストに付けられたより短いデッドラインによって上書きされることがあり、通常は上書きされる。

```proto
rpc Foo(FooRequest) returns (FooResponse) {
  option deadline = x; // グローバルに通用するデフォルトは存在しない
}
```

### リクエストサイズとレスポンスサイズの境界

- リクエストとレスポンスのサイズには上限を設ける必要がある
	- 8MiB程度の制限を推奨。2GiBは多くのprotoの実装が壊れるハードリミット
- 無制限のメッセージは
	- クライアントとサーバーの双方を肥大化させる  
	- 予測不可能な高いレイテンシーを引き起こす
	- 単一クライアントと単一サーバー間の長時間接続に依存することで、回復性を低下させる
- API内のすべてのメッセージを境界付けるいくつかの方法
	- 境界付きメッセージを返すRPCを定義。（この場合、各RPC呼び出しは他のRPC呼び出しから論理的に独立）
	- 制限のないクライアント指定のオブジェクトリストではなく、1つのオブジェクトで動作するRPCを定義。
	- string、byte、またはrepeatedフィールドに無制限のデータをエンコードしない
	- 長時間動作を定義
	- 結果は、スケーラブルな同時読み取り用に設計されたストレージシステムに保存
	- ページ分割APIを使用 ( 「継続トークンなしでページ区切りAPIを定義しない」 を参照) 。
	- ストリーミングRPCを使用。

### ステータスコードの伝搬は慎重に

RPCサービスは、エラーを調査するためにRPC境界で注意を払い、意味のあるステータスエラーを呼び出し元に返す必要がある

例：  

引数をとらないProductService.GetProductsを呼び出すクライアントを考える。
GetProductsの一部として、ProductServiceはすべての製品を取得し、各製品に対してLocaleService.LocaliseNutritionFactsを呼び出すかもしれない。

```
digraph toy_example {
  node [style=filled]
  client [label="Client"];
  product [label="ProductService"];
  locale [label="LocaleService"];
  client -> product [label="GetProducts"]
  product -> locale [label="LocaliseNutritionFacts"]
}
```

ProductServiceの実装が不適切な場合、LocaleServiceに誤った引数を送信し、INVALID_ARGUMENTが発生する可能性がある。  
  
ProductServiceが不用意に呼び出し側にエラーを返すと、ステータスコードがRPC境界を越えて伝播するため、クライアントはINVALID_ARGUMENTを受け取る。しかし、クライアントは ProductService.GetProducts に何の引数も渡していない。つまり、このエラーは役に立たないどころか、大きな混乱を招くことになる 
  
その代わりに、ProductService は、RPC 境界（つまり、実装している ProductService RPC ハンドラ）で受け取ったエラーを照会する必要がある。呼び出し元から無効な引数を受け取った場合は、INVALID_ARGUMENT を返すようにする。下流のものが無効な引数を受け取った場合、INVALID_ARGUMENTをINTERNALに変換してから呼び出し元にエラーを返すべきである。  
  
不用意にステータスエラーを伝播させると、混乱を招き、デバッグに多大なコストがかかることになる。さらに悪いことに、すべてのサービスがクライアントエラーを転送し、何のアラートも発生させないという、見えない停止につながる可能性がある。  
  
一般的なルールとして、RPCの境界では、エラーを問い合わせ、適切なステータスコードで、意味のあるステータスエラーを呼び出し側に返すように注意する。意味を伝えるために、各RPCメソッドは、どのような状況でどのようなエラーコードを返すかを文書化する必要がある。各メソッドの実装は、文書化された API 契約に準拠する必要がある。

### Reapeated フィールドの Tips

#### Repeated フィールド の返し方

- repeated フィールドには hasXxxメソッドは存在しない（repeated フィールドが空の場合、クライアントはそのフィールドがサーバによって入力されなかっただけか、フィールドのバッキングデータが本当に空なのか判断できない）
- 対処：メッセージ内の repeated フィールドをラップすることは、hasXxxメソッドの代替となる

```proto
message FooList {
  repeated Foo foos;
}
```

- より全体的な解決方法は、フィールド読み取りマスクを使用すること
	- フィールドが要求された場合、空のリストはデータがないことを意味する。
	- フィールドが要求されなかった場合、クライアントは応答のフィールドを無視する必要がある。

#### Repeated フィールドの修正（更新）

Bad：クライアントに代替リストを供給することを強制すること。
- クライアントに配列全体の供給を強制することの危険性は何倍にもなる。
- 不明なフィールドを保持しないクライアントは、データ損失の原因となります。
- 同時書き込みはデータ損失の原因となる。
- これらの問題が当てはまらない場合でも、クライアントはドキュメントを注意深く読み、サーバー側でフィールドがどのように解釈されるかを知る必要がある。

修正方法
- 繰り返し更新のマスクを使用し、書き込み時に配列全体を供給することなく、クライアントが配列に要素を置換、削除、挿入できるようにする。
- リクエストプロトで、append, replace, deleteの各配列を個別に作成する。
- リクエストでは、追加またはクリアのみを許可する。
	- これを行うには、repeated フィールドをメッセージでラップする。
	- メッセージがあるが空であればクリア、そうでなければrepeated要素は追加を意味する。

#### repeated フィールドにおける順序の非依存性

```proto
message BatchEquationSolverResponse {
  // Bad: 解決された値は、リクエストで指定された方程式の順序で返される
  repeated double solved_values;
  // (Usually) Bad: solved_valuesの並列配列
  repeated double solved_complex_values;
}

// Good: より多くのフィールドを含むように成長し、他のメソッド間で共有できる独立したメッセージ // リクエストとレスポンスの間の順序依存性がなく、複数のrepeatedフィールド間の順序依存性がない
message BatchEquationSolverResponse {
  // 非推奨。2014年第2四半期までは、この項目が回答として入力され続ける。
  repeated double solved_values [deprecated = true];

  // Good: リクエストの各方程式には一意の識別子があり、
  // 以下のEquationSolutionに含まれている一意の識別子があり、解答は方程式そのものと関連付けることができる。方程式は並行して解かれ、解が作られるとこの配列に追加される。
  repeated EquationSolution solutions;
}
```

### パフォーマンスの最適化

場合によっては、型の安全性や明確性を性能の向上と引き換えにすることができる。

例えば
- 何百ものフィールド、特にメッセージ型フィールドを持つプロトは、より少ないフィールドを持つものよりも解析が遅くなる。
- 非常に深くネストされたメッセージは、メモリ管理だけでデシリアライズに時間がかかることがある。

デシリアライズを高速化するためのテクニック：
- 大きなプロトをミラーリングするが、一部のタグのみを持ち、平行にトリミングされたプロトを作成する。
	- これを、すべてのフィールドが必要でない場合の解析に使用する。
	- トリミングされたプロトはナンバリングの "穴 "が蓄積していくため、タグの番号が一致し続けることを強制するテストを追加する。  
- `[lazy=true]`でフィールドを "lazily parsed "として注釈をつける。  
- フィールドをバイトとして宣言し、その型を文書化する。フィールドを解析しようとするクライアントは、手動でそれを行うことができる。
	- この方法の危険性は、誰かが間違った型のメッセージをbytesフィールドに入れることを防ぐものがないこと。
	- PIIのためにプロトが吟味されたり、ポリシーやプライバシーのためにスクラブされたりするのを防ぐため、ログに書き込まれるプロトでは決してこの方法を取るべきではない

## ref：
- [The Official Buf Style Guide](https://buf.build/docs/best-practices/style-guide/)
- [Developing modules in the Buf Schema Registry](https://buf.build/docs/best-practices/module-development/)
- [Protocol Buffers Documentation - Programming Guides](https://protobuf.dev/programming-guides/)


