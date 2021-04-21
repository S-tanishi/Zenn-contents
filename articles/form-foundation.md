---
title: "form"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [html, php]
published: true
---
### form :データの送信
form要素で囲った中で、
<input>・ <select>・ <textarea>等のタグで、 様々な入力タイプのボタン、つまりフォーム部品を配置する。
	
送信されたデータはWebサーバーに送られる。それぞれ以下で指定。
1.action:指定したファイルにデータの送信と同時に遷移（移動）
2.method:送信属性
3.enctype:送信するデータの形式

get:「クエリ付きURLの情報を“取得する”」
post:「フォームの内容を送信先ページに“送る”」

[他の属性などこちら](http://www.htmq.com/html5/form.shtml)
### input type :フォーム部品の種類を指定するtype属性
タイプ（種類）を指定する。inputタグのみに存在し、必須の属性。
```html:
type="text"     一行テキストボックスを作成する
type="email"    メールアドレスの入力欄を作成するHTML5から追加
type="password" パスワード入力欄を作成する
type="date"     日付の入力欄を作成するHTML5から追加
type="radio"    ラジオボタン
type="checkbox" チェックボックス
type="submit"   送信ボタン
type="button"   汎用ボタン
```
など。[詳しくはこちら](http://www.htmq.com/html5/input.shtml)

### name属性
プログラム側に送る際の目印となり必須
name属性値と入力されたデータ（またはvalue属性の値）がセットでプログラム側に送られる
https://www.php-factory.net/trivia/17.php
### value属性
入力されたデータは実際にはvalue属性の値（value="○○"の○○部分）に反映。
入力→value属性の値なのでtextなどは空or省略、
それ以外の項目ではその性質上必ず必要。

### id属性 for属性
id属性:要素を識別するためのもの
for属性:コントロールのidを指定しフォーム、部品とラベルを関連付け
value属性:フォームの入力コントロールにおける初期値を指定
https://reference.hyper-text.org/html5/attribute/value/
form属性：form要素とラベルを関連付ける
	
### textarea
type="text"が1行のみの入力欄。対してtextareaは複数行の入力欄。
	
### select
nameで名前をつけvalue属性に値を入れている。
```html:
 <select name="status" class="form-control" id="status">
    <option value="良い">良い</option>
    <option value="普通">普通</option>
    <option value="悪い">悪い</option>
 </select>
```
	
### input type="hidden" 隠しデータを設定
表面上は隠せるが、HTMLソースを表示すれば見ることができる点に注意
	
### bootstrapでform "form-group" "form-control"
Bootstrapで基本的なformを作る際
1.<div class="form-group">で囲む。
2.<class="form-control">を定義する。
3.checkbox,radiobutton,submit等はform-groupで囲まない(horizontalでは囲む）。
```html:
<div class="container">
  <form>
    <div class="form-group">
      <label>title</label>
        <input type="text" name="title" class="form-control">
    </div>
        <button type="submit">送信</button>
 </form>
</div>
```

[サイズ変更など詳しくはこちらの記事](https://qiita.com/zaburo/items/8983993d173c51cb3827)
	
### エラー対策（値が送信されていない、受け取れていない）
基本的なことは大丈夫か
1. カラム名は適切か
2. Modelにテーブル情報、カラム名を記載しているか
3. それらはformのname,for,id属性と一致しているか

