# 全部これymlと仮定して

```yml

name: Continuous Integration
// レポジトリのpushイベント時に発火する
on: push // => [push, pull_request] こんな感じで複数イベントの指定も可能

jobs:
  unit-test: // ID名
    name: Unit Test // ジョブ名
    runs-on: ubuntu-latest // ジョブが実行される仮想環境
    steps: // 実行される処理を定義
      // アクションの実行、これだと「actionsのcheckout」というレポジトリの
      // v2.0.0のタグのアクションを実行する
      // 特定のコミットでもアクションを実行できるし、
      // 特定のブランチでもアクションを実行できる
      // 自分のレポジトリのアクションも実行できる
      // - uses:./.github/actions/youraction
      // Docker hub上のイメージもアクションとして実行可能
      // uses:docker://alpine:3.11
      - name: Checkout // ステップの名前
        uses: actions/checkout@v2.0.0
      - name: Set Node.js 12.x
        uses: actions/setup-node@v1.3.0
        with:
          node-version: 12.x
      - name: Instatll dependencies
        // コマンドラインの実行
        run: npm ci
      - name: Test
        run: npm test

  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2.0.0
      - name: Set Node.js 12.x
        uses: actions/setup-node@v1.3.0
        with:
          node-version: 12.x
      - name: Instatll dependencies
        run: npm ci
      - name: Lint
        run: npm run lint

      // ディレクトリを変更可能
      - run:
        workingdirectory: /tmp

      // ジョブが失敗してもワークフローが失敗にはならない
      continue-on-error: true
// 環境変数を設定したい
env:
  DEBUG: true
// もしくは
jobs:
  unit-test:
    env:
      DEBUG: true
// もしくは
steps:
  - env:
      DEBUG: true
    run: echo ${DEBUG}

// ※環境変数を使う場合、GITHUB_XXXは定義してはならない。
env:
  GITHUB_XXX: true
```

# 仮想環境について
一つのジョブ内でのステップ同士は同じ仮想環境を共有することができる。
異なるジョブ間での異なるステップ同士で同じ仮想環境を使うには、「アーティファクト」など別の方法が必要である。

# githubが提供する環境について
Linux, windows, macOSの３つ
この３つに最初からインストールされているソフトウェアがあり、固定ではなく更新されている。
https://help.github.com/en/actions/reference/softwareinstalledongithubhostedrunners

# if文を使って特定のブランチに何らかのアクションが走ったときにデプロイを実行したい

```yml
on: push
jobs:
  deploy:
    // マージ先がmasterであれば
    if: github.ref == 'refs/heads/master'
    steps:
      // ここにデプロイの記述をかく
    steps:
      // pull_requestの時だけこのstepを実行する
      - if: github.event_name == 'pull_request'
       run: ...
```

# コンテナを使う

```yml
  container: node:12.14.1-stretch
  env: // ここで環境変数を使う
  ports: // ポート番号の指定をする
```

# サービスコンテナを使う

```yml
name: Continuous Integration
on: push

jobs:
  unit-test:
    runs-on: ubuntu-latest
    services: // サービスコンテナはここ以下
      postgres:
        image: postgres: 12.1
        env: // このサービスコンテナ内の環境変数をかく
          POSTGRES_USER: pguser
          POSTGRES_PASSWORD: password
        ports:
          - 5432:5432
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s health-retries 5
```

# キャッシュ機能を使ってビルドを早くする
actions/cacheレポジトリで公開してるとのこと。
[URL](https://github.com/actions/cache)
[言語ごとのキャッシュ方法](https://github.com/actions/cache/blob/master/examples.md)
