---
title: "laravel８系でVue.jsを連携するまで 備忘録"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel, vue]
published: true
---
### 前提知識
Laravel6.0以降は自前でVue.jsをインストールし、resources/js/app.jsを編集する必要がある。（前は標準搭載

インストール→コンパイル→VueComponentの作成→app.jsで接続設定→bladeのテコ入れ→連携の確認
## インストール
**npm:パッケージマネージャー**

package.jsonで使用しているパッケージの名前とバージョンに関する情報が書かれていて、それに基づきパッケージがinstallされる。
あと古いモノをアップデートしてくれるらしい。

ルートディレクトリで

```
npm install
```
以上でインストール完了。
ただし同じ環境を作る際は以下の視点が必要
https://blog.minimalcorp.com/users/jigen/posts/6f325dc9b8a00370b6aedf47a34cb3ce

### vuejsは別にコンポーネントのコンパイルが必要
```
npm run dev
```
```
npm run hot
```
hotの場合、Hot Module Replacement(HMR)有効な状態でビルドされ、リアルタイムで編集が反映される


## Vueコンポーネントを作成
resources/jsディレクトリ配下にcomponentsフォルダ作成し
そこにVueコンポーネントを作成(以下例)

```vue:resources/js/components/Sample.vue
<template>
    <div>
        <h1>Sample Component</h1>
    </div>
</template>

<script>
    export default {
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>
```
## app.jsの編集
これまで　
参考：https://www.larajapan.com/2019/10/14/app-jsとbootstrap-js/
https://qiita.com/shonansurvivors/items/1715a483ac4298162ccd

Vue.jsとVueコンポーネント(Sample.vue)を使用するよう定義。

```js:resources/js/app.js
import './bootstrap'
import Vue from 'vue'
import Sample from './components/Sample'

Vue.component('sample-component',require('./components/Sample.vue').default);

const app = new Vue({
    el: '#app',
    components: {
        Sample
    }
});

```
## blade
例を見た方が早いのでwelcome.blade.phpを編集

```html:welcome.blade.php

<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta name="csrf-token" content="{{ csrf_token() }}">

        <title>Laravel</title>

    </head>
    <body>
       
        <div id="app">
            <sample-component></sample-component>
        </div>

    <script src="{{ mix('/js/app.js') }}"></script>
    </body>
</html>
```

**ポイント**

>・<meta name="csrf-token" content="{{ csrf_token() }}">
>・<script src="{{ asset('js/app.js')}}">
>・<div id="app"><sample-component></example-component></div>

それぞれ、
・head内metaタグにcsrf対策のコードの埋め込み
・Vueで描画する領域としてdivタグを設置
・laravel mixがコンパイルする先のファイルの指定を行っている

参考：https://www.ritolab.com/entry/171

## トップページにSample.Vueの内容が表示されているか確認。

http://localhost:8000/

これでLaravelとVueが連携できたことが確認できる
