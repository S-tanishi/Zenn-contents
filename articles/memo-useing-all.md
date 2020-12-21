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
```js:Calendar.vue
実際に表示させる処理