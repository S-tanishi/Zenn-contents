---
title: "フロントとバックの分離"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel, nuxt.js]
published: false
---
### 概要
laravelをAPIとしてフロントとバックを分離。SPA環境を構築
### laravel(API)
#### docker 環境構築
docker これまで通り環境構築

Auth/Admin
  UserController
  LoginController

Kernel.php(middleware-apiに追記)

config
 auth(guards-api 追記)
 cors('allowed_origins' => ['*'],を
 'http://web',と変更)

web.php
 api.php

### nuxt.js
docker
ui yarn create new-app

yyyyy

ディレクトリ完成