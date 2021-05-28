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


### 概要
Reactを調査したのでメモ
 
### [Vue](https://jp.vuejs.org/v2/guide/installation.html) を採用するといい場合
・技術の分離
・デザイナーがHTMLとCSSを担当していて、その作業を行う場合
　　・それまで通りの作業をして、JSに精通するエンジニアにバトンタッチする開発
・機能を実現する程度の部分的なフロント開発
・採用、育成　◎

### [React](https://ja.reactjs.org/)
・[関心の分離](https://ja.wikipedia.org/wiki/%E9%96%A2%E5%BF%83%E3%81%AE%E5%88%86%E9%9B%A2)
・はやさ、軽さ、パフォーマンス優先
・堅牢性　TypeScriptとの相性○
・採用、育成　△　
>Reactはjsxに設計構想 / 方針についての深い理解がないとそもそも触れない
### Vue3
Vue.3.0で導入されるコンポーネントを書くための新しいAPI。
React Hookから影響を受けていて、以下の2点が大きく変わる。

・TypeScriptの全面的サポート
・ロジックの関心をベースにコードを構造化できるようになる
> Reactはライフルサイクルメソッドがもたらした機能的凝集度の低さをhookによって克服し、SoCを実現している

#### 具体例
現状：Options-based API
>「このコンポーネントはdataを2つ持っていて、methodを3つ持っていて...」という書き方になり、コードが断片的になってしまいます。あるロジックを変更しようとした時、コンポーネントファイル中に散らばる関係各所に影響が出る可能性が高くなってしまいます。特に、コンポーネントが複雑になるほど変更しづらくなってしまう。

Vue３：Composition API
>「このコンポーネントがしようとしていることは、X, YとZということです。」
という書き方ができるようになり、ロジックが何をしたいのかということをベースにコードを書けるようになるとのこと。

https://labo.nozomi.bike/article/78
https://qiita.com/102Design/items/ae018dc80a4d879d92a8
