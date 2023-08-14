
ref:  [監視の考え方 〜あるいは可観測性とはなんなのか〜](https://www.estie.jp/blog/entry/2023/02/14/140442)

- Observability を高めることは、プロダクトの品質を高めることそのもの
- Observability が高ければ問題の存在に早く気付け、先回りして対応できますし、実際に問題が発生した際も調査が早く完了し、顧客影響を最小限にすることができる


# 監視の目的

Google の、監視の目的

-   サービスの停止や低下の兆候を示す指標を提供する。
-   サービスの停止や低下、バグ、不正行為を検出する。
-   サービスの停止や低下、バグ、不正行為に対するデバッグをサポートする。
-   キャパシティ プランニングとビジネス目的で長期的な傾向を把握する。
-   変更や追加機能による想定外の影響を明らかにする。

ref :[DevOps 測定: モニタリングとオブザーバビリティ  |  DevOps の能力  |  Google Cloud](https://cloud.google.com/architecture/devops/devops-measurement-monitoring-and-observability?hl=ja#how_to_implement_monitoring_and_observability)

目的は上記のようにたくさんあるが、用途は以下の２つ

## 監視の用途

- Proactive monitoring：ダッシュボード(サマリー)による未来予測
- Reactive monitoring：過去に何が起きたかを詳細に調べる

## Proactive monitoring

何かが起こる前に問題を認識するための監視

- まだ障害には至っていないものを先に検知したいときに使いたい監視
- 一般的にはダッシュボードを構築することが推奨される

### ダッシュボード

- インサイトを得るためのものです。インサイトを得られるか怪しいグラフは全て、ダッシュボードから削除する
- 「状況を把握または予測する」ことが目的

以下４メトリクスを見ると良い＝Googleの実例：**4 Golden Signals** [Google - Site Reliability Engineering](https://sre.google/sre-book/monitoring-distributed-systems/)
-   Latency - リクエストにかかった時間
-   Traffic - 通信量
-   Errors - HTTP Status Code 5xx の割合
-   Saturation - サービスの提供余裕（CPU/Memory/通信量など）

※Web アプリケーションの基本的なメトリクスですが、サービスレベルでの健康状態を見ようとすると結局これらが一番欲しい

## **Reactive monitoring**：

- 大事なポイント：現時点では必要性がわからなくても、とにかくデータは取っておいて、問題発生時に活かす
- データを集めることは容易だが、必要になったときにデータがないとどうしようもない。監視においては、現実的に可能な限りのデータを収集しておくべき（ref: [Monitoring 101: Collecting the Right Data | Datadog](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)）

# 可観測性（Observability）

監視の次の段階として Observability （o11y, 可観測性）が流行っている

**Observability の目的：「アプリケーションがどう動いているか」を知ることそのもの**
- 具体例： APM (Application Performance Monitoring)、エラートラッキングシステム（Sentry など）
- SRE だけでは完成せず、開発者が圧倒的に重要（「アプリケーションが健康に動いているとはどういうことか？」という問いに真の意味で正しく答えられるのは、実際の開発者だけ）

## E2Eの重要性

- E2E テストはユーザの体験そのものなので、サービスの品質をより正しく反映している
- CPU やメモリ使用量のようなプリミティブなメトリクスも重要だが、もっと重要なのは、「サービスが支障なく提供されている」ことそのもの
	- これを測れるのが E2E テストであり、また latency のようなメトリクス
- Observability 時代の監視では、システムが「動いている」こと、ユーザがサービスを「利用できている」ことを見ることが基本になります。そのためには E2E での監視が適している

# Alert

Alertの哲学
-   **アラートは、人間による対応が必須であるものに絞る**
	-   アラートは減らさなければならない。多いアラートは少ないアラートよりもはるかに御し難い
	-   アラートは人間が対応するものに限る。「アクションは不要だが見ておきたい」レベルのものを残しておくと、本当に重要なアラートを見逃す
	-   アラートは人間が対応**できる**ものに限る。指をくわえて嵐が通り過ぎるのを待つだけのアラートなら出さない方が良い
	-   アラートを出す場合、チーム内の誰でも対応できるように手順や注意事項をまとめておくべきである。手順が固まったら自動化してアラート自体を消すのが良い

-   問題が起きているものだけをアラートする
-   **事象ベースでアラートを鳴らし**、そこから原因となるメトリクスを見る
ref: [My Philosophy on Alerting - Google ドキュメント](https://docs.google.com/document/d/199PqyG3UsyXlwieHaqbGiWVa8eMWi8zzAn0YfcApr8Q/edit)

## 大事なこと

- アラートはつい増やしたくなるが、立ち止まって「本当に必要なアラート、人間が対応しなければならない現象とはなんだろう」と考えてみる。
- CPU 使用率より、レイテンシやエラー率など、 E2E での現象の方が（実運用に近いため）実態を表しているのではないか？アラートすべきはそちらかもしれない

#observability