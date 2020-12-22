---
title: "docker"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [docker]
published: false
---
マニュアル
https://emotion92.blog.fc2.com/blog-category-72-1.html
### 基本
dockerは Dockerfileからの命令を受け取り自動でイメージをビルドする
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

### docker-compose.yml

### Dockerfile
Dockerfileはテキストファイルであり、Dockerイメージを作り上げるために実行する命令をこのファイルに含めることができます

FROM命令はイメージビルドのためのベースイメージを設定します。
FROM イメージ名:タグ名 で指定します。

ENV命令はコンテナ内のサーバー環境変数を設定

SHELL命令は

WORKDIR
コンテナ内で cd /app しているようなもの


phpベースイメージ
https://hub.docker.com/_/php
### 文法

build: で指定しているのはビルドコンテキストを指定
build contextとは
docker buildを実行する際の現在の作業ディレクトリのことを指す

ports
volumes: 
ホスト側のディレクトリや名前付きボリュームをコンテナ側へマウントしたい時に指定します。
depends_on
links


tty
enviroment
expose

