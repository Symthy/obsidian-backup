
## SQS

- フルマネージドのメッセージキューイングサービス
	- 「メッセージキューイング」とは個々のサービスやシステムを「メッセージ」を使用して連携する仕組み

![[Pasted image 20230413164000.png]]

### メッセージキューの利点

- メッセージキューを利用すると、お互いのサービスがそれぞれの状況に関係なく（非同期で）処理を進行できるため、サービス間の結合度が低くなる。
	- このように、システムを構成するサービスやコンポーネント間の結合度や依存度を低くし、独立性を高めることを「疎結合」または「デカップリング」という。  
- サービス間の結合度を低くすると以下のようなメリットが生まれる。  
	- 関連するサービスの負荷や処理状況に引きずられずに自身の処理を進行できる  
	- 関連するサービスで障害が発生した場合やアップデートなどの際に影響を受けずに済む  

SQSはサービス同士を橋渡しする役割を持つAWSサービス

## SQS のユースケース

- トラフィックの量が想定できないようなケース  
	- 例：データベースシステムに対して大量にリクエストを投入する場合、データベースシステム側が高負荷になり処理の遅延が発生する可能性がある。SQSを導入すると、データベースシステム側は負荷に応じてキューのメッセージを処理できるため、（負荷がかからず）処理遅延が発生しにくくなる。
- Auto Scalingによって運用負荷を軽減するケース  
	- リクエストが急激に増加した場合、SQSとCloudWatch、Auto Scalingを連携することでサーバーを自動的にスケーリングできる。
	- リクエストの増加によりSQSに滞留したメッセージ数がしきい値を超えたときにCloudWatchがアラートを上げ、Auto Scalingがサーバーを増やします。キューにメッセージがなくなり次第スケールインすることで、コストを抑えた運用が実現できる。
- 優先順位を設定したいケース  
	- SQSではキュー内のメッセージの優先度を変更することはできないが、キューを使い分けることで処理の優先順位を操作できる。
	- 優先度の高いキューと低いキューの2つを用意し、メッセージを送信する側で優先度によってメッセージを投入するキューを使い分ける。メッセージの受信側は、先に優先度の高いキューにあるメッセージを処理し、完了後に優先度の低いキューを処理する。

![[Pasted image 20230413164858.png]]

## SQS の機能

- 標準キューとFIFOキュー  
- ショートポーリングとロングポーリング  
- 遅延キューとメッセージタイマー  
- 可視性タイムアウト  
- デッドレターキュー

### 標準キューとFIFOキュー

※キューの種類は作成時に選択、作成後は変更できない。

- 標準キュー  
	- メッセージの処理順序は保証されず、送信された順番と異なることがある
	- タイミングによっては同じメッセージが二度配信される可能性がある。
	- メッセージの処理順序や、同じメッセージを受信した際の処理については、受信側で制御する必要がある。  
- FIFOキュー  
	- FIFO（First In First Out）=「先入れ先出し」、送信された順番にキューに残っている最も古いものからメッセージを処理
	- メッセージを二重に取得することもない。

標準キューと比べてFIFOキューの方が機能的に優れている分、利用料金はFIFOキューの方が高く設定されている。また、1秒あたりの処理件数は標準キューが優れている

### ショートポーリングとロングポーリング

※「ポーリング（polling）」：機器などに対して、一定間隔で順番に問合せ（データの送信要求など）を行うこと  

キューからメッセージを取得する方式は以下2種類  
- ショートポーリング
	- メッセージがあった場合はメッセージを返し、メッセージがない場合でも即座に「空である」というレスポンスを返す。
	- SQSに対するAPIコールの数が増えやすく、コストが高くなる可能性がある
- ロングポーリング
	- メッセージがあった場合はメッセージを返し、メッセージが空である場合は設定された時間（最大20秒）待つ。時間が経過してもメッセージを得られない場合は、「空」というレスポンスを返す。  
	- APIコール数を抑えることにより、コストを削減できる可能性がある。  

AWSではロングポーリング方式の使用を推奨している

### 遅延キューとメッセージタイマー  

 メッセージの送信者が送信したメッセージを指定時間経過後に受信させたい場合には、「遅延キュー（Delay Queue）」または「メッセージタイマー（Message Timer）」を使用する。  キューに投入されたメッセージは、指定された時間が経過した後に受信可能になる。 
 
- 遅延キュー
	- キュー全体に対して作用
- メッセージタイマー
	- 特定のメッセージに対して作用

### 可視性タイムアウト（Visibility Timeout）  

- 可視性タイムアウトは一度クライアントがメッセージを受信した後、指定時間（デフォルト30秒）が経過するまでそのメッセージが他のクライアントからは見えないようにする。
- 以下を防ぐ目的
	- キューのメッセージは、受信側が明示的に削除指示をしないと削除されない。
	- SQSのメッセージを受信するクライアントが複数ある場合、タイミングによっては、メッセージが削除される前に複数のクライアントでメッセージを受信してしまうことがある。  


![[Pasted image 20230413171057.png]]

[Amazon SQS可視性タイムアウト](https://docs.aws.amazon.com/ja_jp/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)

### デッドレターキュー（Dead Letter Queue）

- データやシステムの都合で必ず処理がエラーになるようなメッセージがあった場合、メッセージはキューに残り続ける。その場合キューにはメッセージが滞留し、受信側ではエラーになる処理をリトライし続けてしまう。  
- SQSでは、一定の回数（1～1000）エラーになったメッセージを通常のキューから分離し、「デッドレターキュー」に配置します。デッドレターキュー上のメッセージは受信さないため、エラーを繰り返すこともない。

![[Pasted image 20230413171109.png]]

#AWS/SQS
