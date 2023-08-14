
- [twitter - Reactのstateはどのように管理されているか](https://twitter.com/sakamoto_582/status/1654045476457709569)
	- 公式Doc： https://react.dev/learn/state-as-a-snapshot
	- ReactはuseStateが呼ばれる度に現在のstateでレンダーしたSnapshotを作成する（それを使う。再レンダリング時に変数とイベントハンドラは新しくなる）