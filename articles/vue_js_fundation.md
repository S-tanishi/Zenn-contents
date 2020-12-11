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
## ディレクティブ
Vue.jsでは標準のHTMLに対して独自の属性を追加することで、属性の値の変化に応じた DOM操作 を行う。
v- から始まるこの特別な属性のことを **ディレクティブ** と呼ぶ
### 条件付きレンダリング(v-if/v-show)
テンプレート内の表示・非表示を切り替えたい時に使う
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
### v-ifとv-showの違い
共に要素の表示の切り替えを行うが、どう実現しているかが違う。
```
v-show: スタイルのdisplayプロパティの値を切り替えている

v-if: 式の結果に応じてDOM要素を追加・削除
紐づけられた変数の真偽値によって指定されているHTML要素の有無を動的に切り替えることができる。
```
使い分けの基準は
切り替えの頻度と初期表示のコスト
スタイルの操作よりもDOM操作の方がレンダリングコストが高くなる
頻繁に式の条件結果が変わる場合はv-showを使うべき（一般的には

## バインディング
### v-bind 
特定の条件が成立する時にUIの見た目を変える、例えば
フォームに不正な値が入力された時に赤く表示するなど、そんな時に使う。

動的に切り替えたい属性に切り替えるための変数を紐づけしておくだけでこれらを実現してくれる

```html:
v-bind:属性名="データを展開した属性値"
v-bind:class="オブジェクト・配列"
v-bind:style="オブジェクト・配列"

// 省略
v-bind:属性名　→ :属性名
```

input要素のvalue属性を紐付ける場合はv-modelを用いると便利である。
### v-bind:class
以下の場合、canBuyが偽であるならclassの属性がerrorに
```html:
<p v-bind:class ="{error: !canBuy"}>
  500円以上から購入できます
</p>
```
大規模になるにつれプロパティの数や値の式が複雑になりメンテナンスが困難に。
その場合、テンプレートに直接記述するのではなく算出プロパティとしてVueインスタンスに移すべし
```js:
computed: {
  errorMessageClass: function () {
    return {
      error: ! this.canBuy
    }
  }
}
```
```html:
<p v-bind:class="errorMessageClass">
  500円以上から購入できます
</p>
```
### v-bind:style
```js:computed内
errorMessageStyle: function () {
  return {
    border: this.canBuy ? '' '1px solid red',
    color: this.canBuy ? '' : 'red'
  }
}
```
```html:
<p :style="errorMessageStyle">
  500円以上から交流できます
</p>
```

### v-for  リストレンダリング
```html:
v-for="要素 in 配列"
v-for="(要素, インデックス) in 配列

v-for="値 in オブジェクト"
v-for="(値, キー) in オブジェクト"
```
配列あるいはオブジェクトのデータをリストレンダリング（繰り返しレンダリング）できる


v-bind:key=〜で生成時に一意なキーを各要素に与える必要がある
これはVueのパフォーマンス等の理由から必要(効率的なリストレンダリングパポーマンス)
```
<!-- data: { arr: ['に','し','ん']}を定義しておく -->
<ul>
  <li v-for="(item, index) in arr" v-bind:key="item">{{ index }}{{ item }}</li>
</ul>
```
```html:レンダリング後
<ul>
  <li>0 に</li>
  <li>1 し</li>
  <li>2 ん</li>
</ul>
```
### v-on イベントハンドリング
ある要素をクリックしたときに特定の処理を行いたい場合、その要素のイベントに処理を割り当てておく必要がある。
つまり、入力に対してmethodsプロパティ内の関数、コンポーネントで用意されている処理を実行する関数を呼び出す。

ボタンを押すと文字の色や大きさを変えたり、要素の表示の切り替えをしたりなどさまざまな時に使う。

```html:
v-on:イベント名="式として事項したい属性値(処理)"
v-on:click → @click
```
### v-model フォーム入力バインディング
UIから入力を受け付け、データを更新するために、
v-on:change(input),とv-bind:valueを使う。

これらを毎度記述してられないので
二つの機能をまとめた双方向のデータバインディングを実現している糖衣構文（記法を簡略なものにした構文）があり、それが v-model 

input要素などのvalue属性をv-bindでバインディングし、changeイベントに対してVueの変数にvalue属性の値を代入することで実装できます

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
