---
title: "memo"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
### v-calendar ライブラリ
参考：https://qiita.com/kanary/items/442bd44c2896a534768b

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

WebのセキュリティについてはSQLインジェクションとCSRFとXSSぐらい覚えておけばいいと思う。

### cors 
https://qiita.com/kobayashi-m42/items/c0a2609ae61a72dcc60f

### Cookie(session) 認証 Token認証 
**Cookie**
Webサーバーが初めて接続した際に預けておく小さなファイル

クライアントが初めて接続した際に
Webサーバーがクライアントに対してCookieファイル（SessionID）を発行し、HTTPレスポンスのヘッダを利用して送ります。その際に発行されたSession情報（SessionID）にはログイン情報が含まれます。

以後、クライアントがWebサーバーへアクセスした際は
リクエストヘッダに含まれるCookie（SessionId）をサーバーが参照し、実際にサーバーに保存されているSession情報と合致した際に認証されたとみなされる。

メリット
・情報の保持
デメリット
サーバー側で情報を持つのでリクエストの回数だけSession情報を保存する。

**token**
まずはCookieと同じく接続した際にTokenが返されるが、その情報をサーバーに保存しない。
「認証に成功した」というToken（いわゆる「認証情報」）を持ち、リクエストする度にリクエストヘッダにTokenを含んで送っています

メリット
・送られてきたTokenの認証だけで処理がすむ
サーバー側では並列分散が容易でスケーラブルになりネイティブアプリ向け

メリットはというと、TokenのデータサイズはCookieのSessionに比べ大きくなります。クライアント側でTokenを管理する場所として、LocalStorageかCookieに保存する方法がありますが、Cookieに保存する際には上限が4kbと制約がつく点は注意が必要になることが挙げられます。

https://magazine.techcareer.jp/technology/skill/11273/
### セキュリティー
SQLインジェクション
不正なSQLを使って脆弱性のあるサイトにinjectionするってこと
それにより
データベースの改竄・漏洩が可能になる

代表的な対策３つ
エスケープ処理
プログラム言語を扱ううえで特別な意味をもつ文字・記号が入力された際に、別の文字列に書き換えてしまうことをさす

WAFの導入
Application Firewallの略で、ウェブサイトをアプリケーションレベルで防御するファイアーウォールのこと

脆弱性診断サービスでチェック


https://www.kagoya.jp/howto/network/sql-injection/