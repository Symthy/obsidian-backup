
# CloudTail

AWS CloudTrail
- AWSサービスに対して「いつ」「誰によって」「どのような操作が行われたのか」を記録・保存するサービス
- 利用登録なしに過去90日間のログを無料で参照可能

監視対象となるイベント
- 管理イベント ... ユーザーのログイン、EC2インスタンスの作成 など  
- データイベント ... S3上のオブジェクト（データ）の操作、Lambda関数の実行  
- インサイトイベント ... 通常と異なる操作（書き込みAPIの呼び出し）

## 証跡情報

- CloudTrailのログは「証跡情報」を登録することによりS3バケットへ保存することができる。
- S3バケットへログを保存することにより、90日を超えてもCloudTrailのログを保存

![[Pasted image 20230226170728.png]]

#AWS 