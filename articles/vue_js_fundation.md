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
データの変更を監視して、それをトリガーに非同期処理や複雑な処理を行う必要がある時に使えるのが、watchプロパティ(監視プロパティ)
```js:
watch:  {
   変更を監視するプロパティ名を記述: {
     handler: function (変更後の値, 変更前の値) {
       ここに処理内容を記述
   },
     [deep: 真偽値,]
     [immediate: 真偽値,]
 }
}
```
deep(任意) : trueの場合、監視するプロパティがオブジェクトの場合ネストされた値の変更も検知します。2.2項で詳しく扱います。
immediate(任意) : trueの場合、初期読み込み時にも呼び出します

このようにdeep、immediateのそれぞれをオブジェクトのキーと真偽値で表現し、コールバック関数をhandlerで記述します。
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
watchプロパティを使うべき場合
computedプロパティでは処理できない非同期通信などの複雑な処理を行う場合
更新前と更新後の値を使う場合
処理を実行しても、データは返さない場合

### methods　プロパティ
computedプロパティ内に記述していた関数をmethodsプロパティに記述し、テンプレート構文でその関数を呼び出すだけ。
```js:
var vm = new Vue({
  el: '#example',
  data: {
    firstNum:3,
    secondNum:4
  },
  methods: {
    // 算出 getter 関数
   calcNum: function () {
      // `this` は vm インスタンスを指します
      return this.firstNum * this.secondNum
    }
  }
})
```
```html:
<div id="example">
  <p>{{ calcNum() }}</p> 
</div>
```
。pタグの要素には常に、calcNumの計算結果の戻り値が表示されるようになります。（methodsプロパティを用いているので必ず()をつけることに注意しましょう。）

methodsプロパティはv-onのハンドラ関数としても利用できるなどの汎用性の高さがメリットとしてあげられます。

computedプロパティは関数内の変数に変更があるときだけ再計算されます。変更がない場合はキャッシュが利用されるため、余計な計算を行うことがなくなるのがメリットです。

データに何かしらの処理を加えてから表示させたい際はcomputedプロパティを用いる。
記述方法はcomputedプロパティ内の関数名と処理内容と戻り値を定義するだけ。
watchプロパティに比べて記述が簡潔で可読性も高い。
methodsプロパティと異なり、キャッシュの利用があるため、関連する変数が変更される場合のみ、関数が実行される。
computedプロパティはライフサイクルダイアグラムでmountedよりも前に位置付けられるので注意する必要がある。

### props プロパティ
親インスタンスから子インスタンス(コンポーネント)へデータを受け渡す時に重要になるのがpropsプロパティ
propsを用いれば親から子への単一方向のデータの流れを実現でき、データの流れを把握しやすくなる

重要なのが
子のscriptタグ内（js記述部分）のpropsで、親から受け取るプロパティ名を列挙
親のtemplateタグ内（html記述部分）で、子の呼び出しとデータの受け渡し

propsの書き方は、文字列の配列かオブジェクトの配列。
propsでの命名においてキャメルケースを用いた場合には、親のhtmlではケバブケースにする必要がある。

```
