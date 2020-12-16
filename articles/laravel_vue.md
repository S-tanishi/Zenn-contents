---
title: "laravel✖️vue.js"
emoji: "🐷"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [vue, laravel]
published: false
---
## laravel と　Vue共存
### laravel Mix
フロントエンドのビルドにはWebback,Gulpなどの設定を行う必要がある。
しかしlaravelには
 JavaScript や Vue コンポーネント、SCSS などをコンパイルする仕組み、つまり容易に設定できるlaravel Mixというライブラリがある

laravel Mixとは
・フロントエンドのアセットをコンパイル、バンドルしてくれるツール
・ツールの中身はwebpackを利用している
・webpack設定ファイルをより分かりやすく簡単に書けるように設定ファイルをラップしている
・lessやsass、babelなどよく使われるローダーが最初から用意されていて、デフォルトで利用することができる
・Laravelを使っていないアプリでも、コンパイル・バインディングのツールとして利用できる
```
composer require laravel/ui
```
bootstrapやvue、reactなどさまざまな
フロントエンドのベースコードを生成できます。
```
php artisan ui vue
```
このコマンドを実行すると、
package.jsonに様々なフロントエンドパッケージが追加されたり、
ベースとなるjsファイルやサンプルのVueコンポーネント、
Laravel Mixの設定ファイルなどが自動で配置されます

必要なフロントエンドパッケージがpackage.jsonに追記されました

```php:webpack.mix.js
mix.js('resources/js/app.js', 'public/js')
```
js
JavaScript と Vue コンポーネントをコンパイルします。
第一引数がコンパイル対象のファイル、第二引数がコンパイル結果の配置先。

これらのパッケージをインストール
(JavaScriptのパッケージとvueをインストール)
```
npm install 

npm install -D vue
```
このコマンドを実行したら、
/node_modules/ ディレクトリが作成され、
その配下に様々なパッケージのディレクトリ、ファイルが追加されます
(.gitignoreされているため　commitには残らない)

### vue Routerのインストール
Vue.jsでSPAを構築するためのルーティング処理を行う
Vue公式のツール
```
npm install --save vue-router
```

### フロントエンドのビルド
```
npm run dev
```
このコマンドを実行することで、
Laravel Mixのビルド処理が実行され、
コンパイルされたjs、cssが
/public/js public/cssに出力されます。

### 実際の処理
初回アクセス時のみlaravel側でリクエストを受けページを表示し
それ以降はフロント側のVue Routerによってルーティングが行われます

その最初のリクエストを受け取る
Laravel側のルーティングとbladeファイルを追加します。
```php:web.php
Route::get('/{any}', function() {
     return view('app');
})->where('any', '.*');
```

### 画面処理
```html:
<head>
〜略〜
<!-- Scripts -->
<script src="{{ mix('/js/app.js') }}" defer></script>
</head>
<body>
<div id="app">
</body>
```

resources/js/app.js を編集

フロントエンドのビルド
$ npm run watch
これにより監視状態になり、ファイルが変更されるとコンパイルされる

### Vue Routerの導入

npm install -D vue-router

ルートコンポーネント resources/js/App.vue を作成
```vue:App.vue
<template>
    <div>
        <main>
            <div class="container">
                <RouteView />
            </div>
        </main>
    </div>
</template>
```
ルートコンポーネントとは、コンポーネントツリーの頂上に位置するコンポーネントです。<div id="app"></div> にこのコンポーネントが描画されます。その他のコンポーネントは直接または間接的にこのコンポーネントから呼び出されることになります。

<RouterView /> に注目してください。このコンポーネントは Vue Router によって提供されています。前章の SPA での「画面遷移」 についての説明を思い出して（もしくは読み返して）ください。「URL によって HTML 部品が切り替わる」と説明しましたが、<RouterView /> が切り替わる箇所を定義します。つまり URL が変わるたびに、<RouterView /> の部分に、URL に対応する HTML 部品が入れ替わって描画されます。

## 認証API 
・会員登録 I/O設計 input（リクエスト）output（レスポンス）

### API用のルート

API と画面のルート定義は別々のファイルに記述した方が分かりやすいので、API のルート定義は前回画面を返却するルートを定義した web.php ではなく api.php に記載しましょう。そのためには少しデフォルトの定義を変更する必要があります。App\Provider\RouteServiceProviderを編集

ポイント
RouteServiceProvider
アプリケーションの起動時にルート定義を読み込むためのクラス

### testの準備
簡単に API の動作を確かめるため
```
docker-compose exec app php artisan make:test XxxTest
```
### ルート定義 api.php
いつも通り

### コントローラー
トレイトに基づいたメゾットの追加

### 動作確認
```
./vendor/bin/phpunit --testdox
```
### 簡単な画面
ポイント
・RouterLink コンポーネント
RouterView と同様、Vue Router から提供されているコンポーネント
画面遷移（サーバーサイドへGETリクエストが）が発生しないアンカーリンク<a>
Vue Router の管理下にあるページへ遷移する場合は必ず RouterLink を使用

componentsフォルダ内に部品(.vue)を作成し
ルートコンポーネントのApp.vueにimport,exportし表示させる

npm run watch で確認 


### vuex:Vue のために開発された状態管理ライブラリ
コンポーネントをまたいで参照したいデータを入れておく場所
この「データを入れておく場所」をストアという。
```
npm install --save-dev vuex
```

#### メリット
親コンポーネントと子コンポーネントの間でのデータのやり取りをショトカできる点

Vue コンポーネント同士がデータをやりとりするためには、基本的に $emit と props を使用した親子間のコミュニケーションで実現する必要がある。

コンポーネントツリーの階層が深く複雑になるとこのやりとりが煩雑になる。

VuexにPI との通信やデータの管理をストアに集中させることで、どのコンポーネントからでもデータを更新・参照できるようになる。
##### vuex ストアの構成要素 4つ
**ステート**
データの入れ物そのものです。ログイン中のユーザーデータなどが該当
**ゲッター**
ステートの内容から算出される値。ユーザーがログイン中であるかどうか
**ミューテーション**
ステートを更新するためのメソッド
ンポーネントはステートを直接変更することができないため、ミューテーションを介してステートを更新。ミューテーションは同期通信でないといけない
**アクション**
ミューテーションと同様にステートを更新するメソッドですが、ミューテーションとの違いはアクションは非同期処理である点です。

### ストア作成
resources/js/store ディレクトリ内にauth.js

app.jsにインポート 
### @csrf対策
laravelの場合,
form以下に@csrf(<input type="hidden" name="_token" value="..." />)
SPAにおけるAPIの場合、
<input> 要素を設置することができない

→CSRF トークンはレスポンスのたびにクッキーに入れて送信されています
クッキーからトークンを取り出して、HTTP ヘッダーにそのトークンを含めてリクエストを送信しても CSRF チェックがかかります。
→クッキーと HTTP ヘッダーを利用する

クッキーの値を取り出す関数を作成→axiosの設定→app.jsで読み込む

### フロントの分離
laravel７からSanctumが推奨
APIトークンを発行するタイプと、SPA認証の2種類を提供するライブラリ