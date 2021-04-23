---
title: "React"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [react]
published: false
---

新しくReact/Nextを学ぶことになったのでアウトプット

公式
https://nextjs-ja-translation-docs.vercel.app/docs/getting-started
## Reactとはなんぞや
```


## それぞれの特徴
Vue.js/Nuxt.js
・学習コストが低い
・デザイナーがフロントエンドに精通していない場合や経験の浅い技術者からすると技術が別れているので着手しやすい
・ルールがゆるい/冗長な表現が少ないため書きやすい

・大規模化したときのコンポーネント管理/サイクル管理等設計,SPA化の難易度/TypeScriptとの親和性

React.js/Next
・Hookによる開発効率の向上(エコシステム)
・機能で分けているのである程度の規模以上なら保守コストの低下
・SSRによるUX向上

・冗長/省略技法/state/設計などのReactの思想の問題＋JSやその他知識がないと難しい

結局のところ両者の違いは思想の違いで、それらの違いがを生んでいる。
好みはReact。美しいから

https://jp.vuejs.org/v2/guide/comparison.html


## Hook 
概要は公式を読むとわかる
https://ja.reactjs.org/docs/hooks-overview.html

一言でいうと、関数コンポーネントでstate管理ができるようになった。

導入以前
関数コンポーネントに状態管理/ライフサイクルをもたせることができなかった。
class コンポーネントに書いていて

state参照にthisを使ったり、メソッドにbindしていたり
それらを行うconstructorを書いたり、renderしたりした。

基本的にフックを使用していき、場合によってはクラス型で書いていくことになる。


bootstrap　導入
```
 yarn add bootstrap bootstrap-react
```
```_app.js
import 'bootstrap/dist/css/bootstrap.min.css'
```
https://react-bootstrap.github.io/components/navbar/


Hookを使うべき6つの理由
https://tyotto-good.com/blog/react-hooks
https://blog.bitsrc.io/6-reasons-to-use-react-hooks-instead-of-classes-7e3ee745fe04


async 非同期関数を定義する関数宣言のこと
https://qiita.com/soarflat/items/1a9613e023200bbebcb3

fetch API
>リクエストやレスポンスといったHttpのパイプラインを構成する要素を操作できるように
https://developer.mozilla.org/ja/docs/Web/API/Fetch_API/Using_Fetch
・元々XMLHttpRequestを使用していた
　・サーバーと対話するためのもの
　　・それらXMLHttpRequestを使って複雑で動的なwenページを構築するプログラミング手法のことをAJAXという


