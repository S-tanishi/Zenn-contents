---
title: "vue.js 基礎"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [vue]
published: false
---
### Vue Component の仕様　.vueファイル
*.vue ファイルは HTML のような構文を使用して Vue コンポーネントを記述するカスタムファイルフォーマットです。各 *.vue ファイルは <template>、<script>、<style> の三つのトップレベル言語のブロックで構成されています。
https://vue-loader-v14.vuejs.org/ja/start/spec.html

1. template内には 
実際に表示させる処理
### v-if
v-ifディレクティブは表示の切り替えをしたい要素に指定することで、指定した値の真偽値によって表示を切り替えるディレクティブ
指定した値がtrueであればその要素は表示され、falseであれば表示されない
```html:<div id="app">
  <p v-if ="isShow">表示されています</p>
  <p v-else>隠し要素が表示されています。</p>
</div>
```
```app.js:app.js
new Vue({
   el:'#app',
   data:{
    isShow:true
   }
})
```
### v-show
v-ifと同じように要素の表示の切り替えを行うが
v-showディレクティブはcssのdisplay要素を切り替えているだけで
v-ifディレクティブのように要素自体が消えたり、生成されたりしているわけではありません。

v-ifディレクティブでは紐づけられた変数の真偽値によって指定されているHTML要素の有無を動的に切り替えることができる。
v-ifディレクティブはDOM操作で要素を切り替える。
v-showディレクティブは要素のdisplayプロパティの値で表示を切り替える。
@click
v-else
2. script内
import
export() {
    computed: {
        //上と
        apiStatus () {
        return this.$store.state.auth.apiStatus
        },
        //下は同じ（だが必要：import { mapState } fro'vuex'）
        ...mapState({
    　　apiStatus: state => state.auth.apiStatus,
    }

    methods:
}

DOM操作

### computed　算出プロパティ
データそのものに何らかの処理を加えたものをプロパティにしたい時に使う
```js:
computed: {
   ここに算出プロパティ名を記述: function () {
      ここに処理内容を記述
      return ここに処理後の表示させる値を記述
 }
}
```
```js:
var vm = new Vue({
  el: '#example',
  data: {
    firstNum:3,
    secondNum:4
  },
  computed: {
    // 算出 getter 関数
   resultNum: function () {
      // `this` は vm インスタンスを指します
      return this.firstNum * this.secondNum
    }
  }
})
```
```html:
<div id="example">
  <p>{{ resultNum }}</p> 
</div>
```
12と表示
### watch　プロパティ
```js:
var vm = new Vue({
 el: '#example',
 data: {
   firstNum: 3,
   secondNum: 4,
   resultNum: 12,
 },
 watch: {
   firstNum: function (val) {
     // `this` は vm インスタンスを指します
     this.resultNum = val * this.secondNum;
   },
   secondNum: function (val) {
     this.resultNum = this.firstNum * val;
   }
 }
})
```
watchの利点は以下の場合
computedプロパティでは処理できない非同期通信などの複雑な処理を行う場合
更新前と更新後の値を使う場合
処理を実行しても、データは返さない場合