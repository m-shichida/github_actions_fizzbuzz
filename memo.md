# 全部これymlと仮定して

--------------------------------------------

name: 'Hello world'
// レポジトリのpushイベント時に発火する
on: push

jobs:
  build:
    // ジョブ名
    name: Greeting
    // 仮想環境
    runs-on: ubuntu-latest
    // 実行される処理を定義
    steps:
      - run: echo 'Hello world'

--------------------------------------------

# 仮想環境について
一つのジョブ内でのステップ同士は同じ仮想環境を共有することができる。
異なるジョブ間での異なるステップ同士で同じ仮想環境を使うには、「アーティファクト」など別の方法が必要である。

# githubが提供する環境について
Linux, windows, macOSの３つ
この３つに最初からインストールされているソフトウェアがあり、固定ではなく更新されている。
https://help.github.com/en/actions/reference/softwareinstalledongithubhostedrunners
