---
title: "docker"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [docker]
published: false
---
マニュアル
https://emotion92.blog.fc2.com/blog-category-72-1.html

スライド
https://www.slideshare.net/zembutsu/what-isdockerdoing

### docker 
"Docker allows you to package an application with all of its dependencies into a standardized unit for software development."

全ての依存関係をパッケージ化して、コンテナとして動かす。

・dockerイメージは、複数のレイヤの積み重ねで構成され、１つのファイルシステムとして使える

・基本になるのは、ペースイメージと言われる様々なLinuxディストリビューションのファイルシステム（centOS,debian,ubuntuなど)

・その上に複数のイメージレイヤ（層）が積み重なり
コンテナを動かすための必要な依存関係をパッケージ化したものがdocker（読み込み専用）

・イメージ・レイヤは親子関係を持ち、見かけ上１つに見える

・コンテナ作成＝コンテナ用レイヤの作成

https://www.slideshare.net/zembutsu/what-isdockerdoing
### 基本
dockerは Dockerfileからの命令を受け取り自動でイメージをビルドする

#### ボリュームとは
ボリュームとは、データを永続化できる場所のこと

・コンテナ内部にデータを保存しても、コンテナ破棄すると消えてしまう

・データを永続化したいときは、コンテナの外にデータを置く必要がある

→その場所のことを、ボリュームと呼ぶ。

https://matsuand.github.io/docs.docker.jp.onthefly/storage/volumes/

https://qiita.com/gounx2/items/23b0dc8b8b95cc629f32

**Dockerfile**
Dockerfile はテキストファイルであり、イメージを作り上げるために実行するコマンドラインコマンドを、すべてこのファイルに含めることができる
```docker:
# Comment
```

環境変数は以下に示す Dockerfile 内の命令においてサポートされる。
ADD
COPY
ENV
EXPOSE
LABEL
USER
WORKDIR
VOLUME
STOPSIGNAL

ADD XX CCC AAA &&\
この&&で一括してADDを行える

### docker-compose.yml

### Dockerfile
Dockerfileはテキストファイルであり、Dockerイメージを作り上げるために実行する命令をこのファイルに含めることができます

FROM命令はイメージビルドのためのベースイメージを設定
FROM イメージ名:タグ名 で指定します。

ENV命令はコンテナ内のサーバー環境変数を設定

SHELL命令は
シェル形式のコマンドに使用されるデフォルトのシェルをオーバーライドすることができる
 Linuxのデフォルトのシェルは["/bin/sh", "-c"] 、Windowsでは["cmd", "/S", "/C"]
 SHELL命令は、Windows上で特に有用です
https://riptutorial.com/ja/docker/example/11016/シェル命令

WORKDIR
コンテナ内で cd /app しているようなもの


phpベースイメージ
https://hub.docker.com/_/php
### 文法
#### build
build: で指定しているのはビルドコンテキストを指定
build contextとは
docker buildを実行する際の現在の作業ディレクトリのことを指す
Dockerfileの場所 

ports
#### volumes: 
ホスト側のディレクトリや名前付きボリュームをコンテナ側へマウントしたい時に指定します。
ホストOSとコンテナ内でソースコードを共有

例えて言うなら拡張データ領域

#### enviroment
コンテナの環境変数を追加
env_file との違いはdocker-comose.yamlに直接記述する点

build オプションと合わせて指定すると、build中は enviroment で指定した、環境変数は見えないことに注意。
build に変数を渡す場合は args オプションで指定する

#### depends_on
コンテナの依存関係を定義する。
依存とは起動の順序に関係し、下記の例ではdbが起動してからwebが起動する
```dockerfile:
# appはdbに依存する
app:
  depends_on:
      - db
```
links

#### network:
ビルド中にRUN命令のために接続するネットワークコンテナ

#### networks:
networks を最上位として，使用するネットワークを指定
```dockerfile:
services:
  some-service:
    networks:
     - some-network
     - other-network
```
##### command コンテナ起動時のコマンド

#### tty(標準出力の接続先デバイス)
ポート待受とかしていないコンテナをdocker-compose upで起動するとコンテナがすぐ終了してしまう
docker-compose up でコンテナを起動させた際に、
コンテナがすぐに終了してしまうのを防ぐことができます

#### stdin_open
コンテナの標準入力をオープンしたままにする
#### expose
コンテナ内に限定して公開するポートを指定する
公開されたポートは linked servicesからのみアクセス可能

ホスト向けに公開したいのならportを使用
 
#### links
コンテナと他のサービスを繋げる

#### restart
コンテナがすでに存在していた際の挙動を指定する

https://knowledge.sakura.ad.jp/5736/

### CMD ENTRYPOINT 違い
ENTRYPOINT : 必ず実行
CMD : デフォルトの引数

コンテナを起動した際にbash(/bin/bashがコンテナ内で PID１として)が起動するのは
CentOS用の Docker 公式イメージで/bin/bashを実行する命令があるから

https://pocketstudio.net/2020/01/31/cmd-and-entrypoint/
### memo
composerを早くするプラグイン
http://blog.tojiru.net/article/432944706.html

```
// install
$ composer global require hirak/prestissimo

//uninstall 
$ composer global remove hirak/prestissimo
```

