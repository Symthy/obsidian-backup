

Circle CI 上 で docker コマンドを使用し、コンテナを動かしたい場合は  setup_remote_docker を使うとできる

ただし
- (CircleCIの)ローカルマシンのボリュームをマウントすることが出来ない　[CircleCI](https://circleci.com/docs/docker-compose/#using-docker-compose-with-docker-executor)
	- テスト実行等でなら使用できるが、媒体生成しても取得できない
- Docker in Docker ではなく、別ホストでDocker実行しているらしい（だからdockerコマンドが使える。マウントはできない

```yaml
version: 2.1

executors:
  ubuntu:
    docker:
      - image: cimg/base:stable-20.04

jobs:
  hello:
    executor: ubuntu
    steps:
      - checkout
      - setup_remote_docker
      - run:
          command: |
            docker run --rm ubuntu:20.10 /bin/echo 'Hello, World!'
workflows:
  version: 2
  hogehoge:
    jobs:
      - hello
```

ref
- [CircleCI 内で Docker のコマンドを使いたい場合](https://obel.hatenablog.jp/entry/20211105/1636052400)
- [CircleCI の setup_remote_docker を設定した時に留意すべきこと](https://inokara.hateblo.jp/entry/2019/11/24/002413)
- [CircleCI の setup_remote_docker を設定した時に留意すべきこと 〜 追記 〜](https://inokara.hateblo.jp/entry/2019/11/25/000021)

#CircleCI