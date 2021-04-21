---
title: "フロントとバックの分離"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel, nuxt.js]
published: false
---
分ける方法、Sanctumで利用or使わない
### 概要
laravelをAPIとしてフロントとバックを分離。SPA環境を構築
### laravel(API)
**APIとは**
API Apprication Programing Interface

インターフェイス：「何か」と「何か」をつなぐもの

「アプリケーション、ソフトウェア」と「プラグラム」をつなぐもの
APIはソフトウェア同士を繋げます
#### docker 環境構築
docker これまで通り環境構築(略)

ポイントはdocker-compose.ymlにnetworks を記述する点

networks: 

その他
Auth/Admin
  UserController
  LoginController

Kernel.php(middleware-apiに追記)

config
 app(ja)
 auth(guards-api 追記)
 cors('allowed_origins' => ['*'],を
 'http://web',と変更)

web.php
 api.php

Seeder作成
  DatabaseSeeder
　UserstableSeeder

### nuxt.js

linux 最新化,gitのインストール、npm最新化、vue-cli インストール　をしている。
```dockerfile:
RUN 
    apk update && \ 
    apk add git && \
    npm install -g npm && \
    npm install -g vue-cli
```
docker
ui yarn create new-app

https://ja.nuxtjs.org/docs/2.x/get-started/installation/

```
? Project name: blog
? Programming language: JavaScript
? Package manager: Yarn
? UI framework: Bootstrap Vue
? Nuxt.js modules: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Linting tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Testing framework: None
? Rendering mode: Single Page App
? Deployment target: Server (Node.js hosting)
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? What is your GitHub username? nagi125
? Version control system: None

🎉  Successfully created project blog
```

yyyyy

ディレクトリ完成

$ docker-compose exec web yarn add @nuxtjs/auth @nuxtjs/axios @nuxtjs/proxy

docker-compose exec web yarn add --dev sass sass-loader fibers

nuxt.config.js
 APIとの疎通設定

token認証 Session認証

Session認証はブラウザ側のクッキーにSessionIDもたせる処理だから

フロントとバックが別れていても、通常のLaravelの認証処理を書いてあげればいけるんじゃないか？的な推測で

20:25 のず CSRFというセキュリティの問題もあるので、できればSanctum使った方がいい。
20:25 のず けどCORSでそれは弾けるので別にこれでもいいかなと。
20:25 のず セキュリティは重ねて対策をしておいたほうがいいので、Sanctum使えって言われてる理由はそこにあるかな。
