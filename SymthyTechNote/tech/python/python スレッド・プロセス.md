
 [Python multiprocessing vs threading vs asyncio](https://tech.jxpress.net/entry/2021/07/29/101150)
 - CPU負荷の高い処理（いわゆるCPU bound）を達成する必要がある場合
	 - マルチプロセス
		 - multiprocessing
- I/O待ち時間が大きいもの（いわゆるI/O bound）を達成する必要がある場合
	- マルチスレッド（シングルプロセス）
		 - threading
	 - 非同期I/O（シングルプロセス）
	 - asyncio
 - プロセスを作る際に発生するコスト ＞ スレッドを作るコスト ＞ 非同期I/Oのイベントを発火するコスト
	 - 新しく作られたプロセスの数に比例してファイルディスクリプタ数、OSがCPUを切り替えるためのスイッチング回数が大きくなる

その他
- [[Python] スレッドについて - concurrent.futures](https://zenn.dev/bluesilvercat/articles/c492339d1cd20c#concurrent.futures)