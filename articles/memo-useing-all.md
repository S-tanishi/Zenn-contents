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