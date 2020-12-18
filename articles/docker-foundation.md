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

### 文法

build
ports
volumes
depends_on
links


tty
enviroment
expose
