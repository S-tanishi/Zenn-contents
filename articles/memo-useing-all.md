---
title: "memo"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
docker-laravel-vue

npm installもdocker

npm run watch でエラー

→dockerで環境を作ってるんだから、docker-compose のコンテナで起動しないとエラーはく

docker-compose exec web npm install 

docker-compose exec web npm install -D vue

docker-compose exec web npm install --save vue-router

docker-compose exec web npm run watch

で解決

分ける方法、Sanctumで利用or使わない


### v-calendar ライブラリ
参考：https://qiita.com/kanary/items/442bd44c2896a534768b

npm install v-calender でインストール

main.jsについては、Vueにv-calendarを読み込ませているだけ
```js:main.js
import Vue from 'vue'
import App from './App.vue'

import VCalendar from 'v-calendar'

Vue.use(VCalendar) 
Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')
```
App.vueのtemplate内にて、今回カレンダーを作成したCalendar.vueコンポーネントを呼び出す
```js:App.vue
<template>
  <div id="app">
    <calendar/>
  </div>
</template>

<script>
import calendar from './components/Calendar.vue'

export default {
  name: 'app',
  components: {
    calendar
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```
実際に表示させる処理
```js:Calendar.vue
<template>
  <div>
    <v-calendar
    :columns="2"
    title-position="center"
    >
    <template slot='header-title' slot-scope='props'>
      {{props.yearLabel}}年{{props.monthLabel}}
    </template>
    <template slot='day-content' slot-scope='props'>
      <div class="cell-header">
        {{props.day.day}}
      </div>
      <div class="cell-content">
      <template 
      v-if="dateList.some(date => date.ymd === dateToYYYYMMDD(props.day.date))">
        <div 
        class="cell-content-line" 
        v-for="content in getContentFromKey(dateToYYYYMMDD(props.day.date))" 
        v-bind:key="content">
          ・{{content}}
        </div>
        </template>
      </div>
    </template>

    </v-calendar>
  </div>
</template>

<script>
export default {
  name: 'calendar',
  data() {
    return {
      dateList: [
        {ymd: '20190923', contents: ['髪を切る','面談','靴を買う']},
        {ymd: '20190914', contents: ['結婚式']},
        {ymd: '20190901', contents: ['妹の誕生日']},
        {ymd: '20190817', contents: ['海に行く！', '野球の練習']}
      ]
    }
  },
  computed: {
  },
  methods: {
     dateToYYYYMMDD: function(date) {
      let y = date.getFullYear()
      let m = ('00' + (date.getMonth()+1)).slice(-2)
      let d = ('00' + date.getDate()).slice(-2)
      let result = y + '' + m + '' + d
      return result
    },
    getContentFromKey: function(key) {
      const target = this.dateList.find((date) => {
        return (date.ymd === key)
      })
      return target.contents
    }
  }
}
</script>

<style scoped>
.cell-content {
  text-align:left;
  width: 70px;
  height: 50px;
  font-size: 50%;
  /* border: 1px solid #efefef; */
}
.cell-content-line {
  border-bottom: 1px solid #efefef;
}
</style>
```
data部分
これらの配列が今回表示したいデータ。ymdがキーとなる

カレンダーを呼び出す際にプロパティ指定
２列のカレンダーとし、ヘッダを中央に寄せるような設定
```html:
<v-calendar
    :columns="2"
    title-position="center"
    >
```

props.day.dateから取得した日付オブジェクトがdateListに存在するかどうかを見ています

```html:
v-if="dateList.some(date => date.ymd === dateToYYYYMMDD(props.day.date))"
```

存在している日付に限り、中の
```html
        <div 
        class="cell-content-line" 
        v-for="content in getContentFromKey(dateToYYYYMMDD(props.day.date))" 
        v-bind:key="content">
          ・{{content}}
        </div>
```
が評価されcontentが描写される

公式：https://vcalendar.io/installation.html

npm install v-calendar

Import and use VCalendar

### API
API Apprication Programing Interface

インターフェイスとは、コンピュータ用語でいうと、「何か」と「何か」をつなぐもの

「アプリケーション、ソフトウェア」と「プラグラム」をつなぐもの
APIはソフトウェア同士を繋げます

APIを公開する、とはわかりやすく言うと、ソフトウェアにAPIという外部とやりとりする窓口を作り、外部アプリとコミュニケーションや連携ができる状態にする、ということ

### laravel nuxt.js 
laravek Api
```php:routes/api.php 
// 認証済の管理者のみAPIを実行できるように
Route::group(["middleware" => "api"], function () {
    Route::post('/login', 'Auth\LoginController@login');
    Route::get('/current_admin_user', function () {
        return Auth::user();
    });
    //追加
    Route::group(['middleware' => ['auth:api']], function () {
        //ここに認証が必要なパスを書いていく
    });
});
```
```
php artisan make:controller Api/AdminUserController --resource --api
```
```php:routes/api.php 
// 管理者のCRUD機能用のルーティングを作成
Route::group(["middleware" => "api"], function () {
    Route::post('/login', 'Auth\LoginController@login');
    Route::get('/current_admin_user', function () {
        return Auth::user();
    });
    Route::group(['middleware' => ['auth:api']], function () {
        //追加
        Route::apiResource('admin_users', 'Api\AdminUserController')->except(['show']);
    });
});
```

### アプリケーションサーバ(app)コンテナ

├── infra
│   └── php
│       ├── Dockerfile
│       └── php.ini # PHPの設定ファイル
├── backend # Laravelをインストールするディレクトリ
└── docker-compose.yml

```docker:docker-compose.yml
services:
  app: # => サービス名は任意
    build: ./infra/php
    volumes:
      - ./backend:/work
```
build: で指定しているのはビルドコンテキストを指定
build contextとは
docker buildを実行する際の現在の作業ディレクトリのことを指す

Dockerfile が置かれている ./infra/php ディレクトリをビルドコンテキストとして指定します
Dockerビルドの際は Dockerfile のファイルを探すので、ファイル名の指定は不要

volumes: ではホスト側のディレクトリや名前付きボリュームをコンテナ側へマウントしたい時に指定します。
今回はホスト側の ./backend ディレクトリをappサービスのコンテナ内 /work へマウントしてます。

**./docker/php/Dockerfile を作成**
Composerコマンドのインストール
Laravelで必要なPHP拡張機能のインストール
bcmath, pdo_mysql が不足しているので追加インストール
```dockerfile:dockerfile
FROM php:7.4-fpm-buster
SHELL ["/bin/bash", "-oeux", "pipefail", "-c"]

ENV COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer

COPY --from=composer:1.10 /usr/bin/composer /usr/bin/composer

RUN apt-get update && \
  apt-get -y install git unzip libzip-dev libicu-dev libonig-dev && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* && \
  docker-php-ext-install intl pdo_mysql zip bcmath

COPY ./php.ini /usr/local/etc/php/php.ini

WORKDIR /work
```
**php.ini はPHPの設定ファイル**
PHPエラーメッセージの設定
PHPエラーログの設定
メモリ等の設定(お好みで)
タイムゾーン設定
文字コード設定

```
zend.exception_ignore_args = off
expose_php = on
max_execution_time = 30
max_input_vars = 1000
upload_max_filesize = 64M
post_max_size = 128M
memory_limit = 256M
error_reporting = E_ALL
display_errors = on
display_startup_errors = on
log_errors = on
error_log = /dev/stderr
default_charset = UTF-8

[Date]
date.timezone = Asia/Tokyo

[mysqlnd]
mysqlnd.collect_memory_statistics = on

[Assertion]
zend.assertions = 1

[mbstring]
mbstring.language = Japanese
```
### webコンテナ
nginxウェブサーバーコンテナを作成します。
nginxのベースイメージをそのまま利用し
```dockerfile:docker-compose.yml
  web:
    image: nginx:1.18-alpine
    ports:
      - 10080:80
    volumes:
      - ./backend:/work
      - ./infra/nginx/default.conf:/etc/nginx/conf.d/default.conf
    working_dir: /work
```
https://hub.docker.com/_/nginx

port
nginxへ外(ホスト側)からコンテナ内へアクセスさせるため公開用のポートを設定します。
ホスト側:コンテナ側 と設定

default.conf
Laravel公式にnginxの設定例が用意されている
https://readouble.com/laravel/7.x/ja/deployment.html
```
server {
    # 省略
}
```

### dbコンテナ
```dockerfile:docker-compose.yml
    build: ./infra/mysql
    volumes:
      - db-store:/var/lib/mysql
volumes:
  db-store:
```
```dockerfile:dockerfile
FROM mysql:8.0

ENV MYSQL_DATABASE=laravel_local \
  MYSQL_USER=phper \
  MYSQL_PASSWORD=secret \
  MYSQL_ROOT_PASSWORD=secret \
  TZ=Asia/Tokyo

COPY ./my.cnf /etc/mysql/conf.d/my.cnf
RUN chmod 644 /etc/mysql/conf.d/my.cnf
```
**my.cnf**
文字コードの設定
タイムゾーンの設定
ログ設定
```:my.cnf
[mysqld]
# character set / collation
character_set_server = utf8mb4
collation_server = utf8mb4_0900_ai_ci

# timezone
default-time-zone = SYSTEM
log_timestamps = SYSTEM

# Error Log
log-error = mysql-error.log

# Slow Query Log
slow_query_log = 1
slow_query_log_file = mysql-slow.log
long_query_time = 1.0
log_queries_not_using_indexes = 0

# General Log
general_log = 1
general_log_file = mysql-general.log

[mysql]
default-character-set = utf8mb4

[client]
default-character-set = utf8mb4
```

```
# イメージの指定
FROM node:10.14.1-alpine

# コンテナ内で cd /app しているようなもの
WORKDIR /app

# コマンド実行
# linux 最新化,gitのインストール、npm最新化、vue-cli インストール　をしている。
RUN apk update && \
    apk add git && \
    npm install -g npm && \
    npm install -g vue-cli

ENV HOST 0.0.0.0
EXPOSE 8080
```

### npm yarn
Node.jsで動作するパッケージマネージャー

npm
Node.jsをインストールすれば一緒にインストールされる。
2009年にNode.jsがリリースされた翌年にnpmがリリースされた

yarn
2016年にリリース。npmと互換性があり、npmで使用していたプロジェクト設定ファイル（package.json）がそのまま使える。
npmと比べてインストールが速い、セキュリティが高いという特徴がある。
セキュリティが高いというのは、インストール時にパッケージが不正に変更されていないかなどをチェックサムを用いて検証することができ、安全なパッケージのインストールが可能であるということ。
バージョン管理についても優れていて、yarnではプログラムのインストール後に、yarn.lockというファイルが作成され、それにはインストールしたプログラムが使用している別のプログラム（依存プログラム/パッケージ）のバージョンが明確に書き込まれている。
依存プログラム/パッケージをその後再度インストールしてもバージョンの整合性が保たれるので、バージョン不一致でプロジェクトが動かなくなる危険性が無くなる。
npm、homebrew、MacPortsからインストールできる

npmとyarnの違い

依存プログラム/パッケージのケア

npm：バージョン違いの依存プログラム/パッケージをインストールしてしまう可能性あり
yarn：yarn.lockファイルにより、バージョン違いの依存プログラム/パッケージのインストールは起こらない
インストール速度

npm：遅い
yarn：早い

laravelのAPI化
```php:api.php
// 管理画面の機能として実装するのでPrefixにadminをつける
Route::prefix('admin')->group(function() {
    // 認証処理
    Route::prefix('auth')->group(function () {
        Route::post('/login', [LoginController::class, 'login']);
    });

    Route::middleware('auth:api')->group(function () {
        Route::get('/user', function () {
            return Auth::user();
        });

        // Users　
        Route::resource('users', UserController::class)->only([
            'index'
        ]);
    });
});
```

Auth/Admin UserCpntroller作成
LoginController作成

config/app.php設定
auth.php設定
cors.php

seeder

### laravel Auth
**Auth**
認証（Ahthentication）や認可（Ahthorization）の意味。
Laravelでは、デフォルトで認証機能があり、コマンドで作れる。
**Jetstream**

https://qiita.com/ucan-lab/items/7824d1293fef4698c212

https://qiita.com/manbolila/items/498aae00f3574c72f031

Laravel8の認証ライブラリ。
Laravel〜5：make:auth、Laravel6〜7：laravel/uiに分離。Laravel8では、全部入り（Jetstream）になったんだとか。。
Jetstream がやってくれることは、 ログイン、新規登録、メール検証、２段階認証、セッション管理、APIサポート(Laravel Sanctum)、チーム管理など。めっちゃやってくれる。。
```
① Jetstream のインストール
composer require laravel/jetstream    

② セットアップ ： JSのパッケージをインストール
（ livewire と inertia を選択）
php artisan jetstream:install livewire

php artisan jetstream:install inertia          // vueやreactなどを使う場合は、コッチらしい。。

③ マイグレーション実行
% php artisan migrate            // usersテーブル、password_resetsテーブル作成のマイグレーションファイルが、自動生成されてる

④ JSとCSSをビルド
% npm install && npm run dev    // npmコマンドが無いエラーが出たら、Nodeのインストールが必要（https://nodejs.org/ja/）
  // 若しくは、別々にNode.js と NPM をインストール
  % npm install
  % npm run dev
// public/css/app.css、 public/js/app.js2ファイルが作成される
```

