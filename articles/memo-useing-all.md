---
title: "memo"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
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

# イメージの指定
FROM node:10.14.1-alpine

# コンテナ内で cd /app しているようなもの
WORKDIR /app

# コマンド実行

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

### Alpine-Linux
https://yoshinorin.net/2016/10/01/alpine-linux/

### php-fpm
https://qiita.com/kotarella1110/items/634f6fafeb33ae0f51dc

FPM:FastCGI Process Manager
PHP の FastCGI 実装のひとつで、 主に高負荷のサイトで有用な追加機能を用意
CGI: Commom Gateway interface
ウェブサーバ上でユーザプログラムを動作させるための仕組み

Web サーバー 上で PHP ( 動的コンテンツを生成する言語 ) を動作せるための仕組み

https://www.php.net/manual/ja/install.fpm.php

### php apache モジュール
https://www.fumi.org/neta/201205sv.html

### PHP variant
cli, apache, fpm, ztsの４種類

### php8.0 error 解決
composerのバージョンが古かったので2.0に
それだけではエラーが出る
削除
composer global require hirak/prestissimo
追記
mkdir /var/log/php