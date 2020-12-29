---
title: "フロントとバックの分離"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel, nuxt.js]
published: false
---
分ける方法、Sanctumで利用or使わない
### 概要
laravelをAPIとしてフロントとバックを分離。SPA環境を構築
### laravel(API)
そもそもAPIとは
### API
API Apprication Programing Interface

インターフェイス：「何か」と「何か」をつなぐもの

「アプリケーション、ソフトウェア」と「プラグラム」をつなぐもの
APIはソフトウェア同士を繋げます

APIを公開する、とはわかりやすく言うと、ソフトウェアにAPIという外部とやりとりする窓口を作り、外部アプリとコミュニケーションや連携ができる状態にする、ということ
#### docker 環境構築
docker これまで通り環境構築

Auth/Admin
  UserController
  LoginController

docker-compose exec api php artisan make:controller Api\Admin\UserController

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

yyyyy

ディレクトリ完成