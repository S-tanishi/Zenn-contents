---
title: "[Laravel] Form ファザード"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: true
---
実務でlaravelを使って開発をする際、頻繁に使用するので簡単にまとめてみました。
## Formファザード
https://github.com/LaravelCollective/html
```
composer require laravelcollective/html
```
## 基本
```
{{ Form::○○(name属性, value属性,[オプション]) }}
```
### old関数 
```
old(前sessionのデータ,初期値)
```
Webのフォームは一時的に情報を残すsessionの仕組みがある。
でold関数はそれを取り出す、つまり前の情報を取り出す役目がある
### validation error
エラー文面をコンポーネント化してあると良い
```php:
@includeWhen($errors->get('name'), '._components.validation_error', ['errors' => $errors->get('name')])
```
### formを作る際の骨格
```php:
{{ Form::open(['route' => ['task.store'], 'method' => 'post']) }}
@csrf
@method('post')
    {{-- Form --}}
    {{ Form::submit('送信', ['class' => 'btn btn-primary']) }}
{{ Form::close() }}
```
## label
```php:
// <label for="name">氏名</label>
{{Form::label('name','氏名')}}
```
## text
```php:
// <input type="text" class="form-control" placeholder="例）たにし 太郎">
{{ Form::text('name', old('name', $hoge->name ?? ''), ['placeholder' => '例）たにし 太郎','class' => 'form-control', 'required' => 'required']) }}
```
```php:
{{ Form::text('title', $hoge->title, ['required' => 'required', 'readonly']) }}
```
## textarea
```php:
// <textarea class="form-control" id="exampleFormControlTextarea1" rows="3"></textarea>
{{ Form::textarea('question1', old('question1', $hoge->question1 ?? ''), ['class' => 'form-control', 'rows' => '3']) }}
```
```php:サニタイズver：そのまま送るとJsが動いてしまうような特殊記号（=, ?など)をHTMLに変換
{!! Form::textarea('message', old('message', $hoge->message ?? ''), ['class' => 'form-control', 'rows' => '5']) !!}
```
第一：name 　第二：value
なお改行データは |n と送られるので、取得するときにはn｜2brを使って変換する必要がある
```
{!! nl2br($hoge->message) !!}
```
## select 
１０個くらいまではそのまま。それ以上はselect2やv-selectなど使うと良い
```html:
<select class="form-control" id="exampleFormControlSelect1">
      <option>1</option>
      <option>2</option>
</select>
```
```php:
{{ Form::select('hoge_category_id', $hogeCategories, old('hoge_category_id', $hoge->hoge_category_id), ['class' => 'form-control']) }}
```
第一：name　第二：value[] 　第三：デフォルト値（select状態）
valに配列を選択すると自動でやってくれる。

選択してください系はutilityなどでgetTargetColumnSortAssocなど作成し、id,nameに加工したあとわたすとよし
### valをおくりたいとき
```php:
<select class="select-style" id="hoge_time" name="hoge_time">
@foreach($hogeTimes as $key => $val)
  @if($val == old('hoge_time', $hoge->hoge_time))
    <option value="{{ $val }}" selected>{{ $val }}</option>
  @else
    <option value="{{ $val }}">{{ $val }}</option>
  @endif
@endforeach
</select>
```
idが違うとだめ送れない
## radio 
項目が固定でかつ４つまでなら採用
nameとvalueで指定した値が一組になって送信される
```html:
<div class="form-check">
  <input class="form-check-input" type="radio" name="exampleRadios" id="exampleRadios1" value="option1" checked>
  <label class="form-check-label" for="exampleRadios1">Default radio</label>
</div>
```
```php:
@foreach($hogecategories as $key => $val)
  {{ Form::radio('hoge_category_id', $val, ($key == old('hoge_category_id', $hoge->hoge_category_id)), ['id' => 'hoge_category_id'.$key, ]) }}
  {{ Form::label('hoge_category_id'.$key, $val, ['class' => 'form-check-label']) }}
@endforeach
```
```php:
@foreach(EnumHoge::IS_HOGE as $key => $val)
  <li class="form-check">
    {{ Form::radio('is_hoge', $key, ($key === old('is_hoge')), ['id' => 'is_hoge'.$key, 'class' => 'form-check-label' 'required' => 'required']) }}
    {{ Form::label('is_hoge'.$key, $val) }}
  </li>
@endforeach
```
第一：name　第二：value　第三：デフォルト　チェック　第四：オプション

label の第一引数：```hoge_category_id.key``` は、label forに該当。
for属性にidを指定すると、そのラジオボタンに紐付いたname-idとなる。

radio :  true or falseでチェックを入れるか判断。1 = True と扱う場合がある(今回の場合はold)
```php:どれも同じ意味
if( $hoge == 1 ){
}else{
}
$fuga = ($hoge == 1) ? A(true) or B(false)
$fuga = ($hoge == 1)  if文は省略できる
```
## checkbox
```html:通常
<div class="form-check">
  <input class="form-check-input" type="checkbox" value="" id="flexCheckDefault">
  <label class="form-check-label" for="flexCheckDefault">
    Default checkbox
  </label>
</div>
<div class="form-check">
  <input class="form-check-input" type="checkbox" value="" id="flexCheckChecked" checked>
  <label class="form-check-label" for="flexCheckChecked">
    Checked checkbox
  </label>
</div>
```
```php:単一
<div class="col-md-3 my-auto ml-4">
  {{ Form::checkbox('is_hoge', true, old('is_hoge', $hoge->is_hoge ?? true),  ['id' => 'is_hoge', 'class' => 'form-check-input']) }}
  {{ Form::label('is_hoge', 'hogeあり', ['class' => 'form-check-label']) }}
</div>
```
```php:複数
<div class="row">
  @foreach($hogeTags as $key => $val)
    <div class="col-md-2 py-1 ml-4 my-auto">
      {{ Form::checkbox('tags[]', $key, in_array($key, old('tags', $hoge->hogeTags->pluck('id')->toArray())), ['id' => 'tag'.$key, 'class' => 'form-check-input']) }}
      {{ Form::label('tag'.$key, $val, ['class' => 'form-check-label']) }}
    </div>
  @endforeach
</div>
```
```php:表で使うようなパーツ/inarrayの引数の仕様でこうなる。
@foreach(EnumHoge::INTERESTS as $key => $val)
  <li class="form-box__harf-layout__item">
    {{ Form::checkbox('interests[]', $key, (is_array(old('interests')) && in_array($key, old('interests'))) ? 'checked="checked"' : '' , ['id' => 'interest'.$key, 'required' => 'required']) }}
    {{ Form::label('interest'.$key, $val) }}
  </li>
@endforeach
```
第一： name属性　第二：value値　第三：デフォルトの checked　第四：追加属性（連想配列形式）

こいつにはonoff 機能がなく、on機能しかない。
→コントローラー側で処理を加えないといけない。
false値を渡して、onのときにtrueになる処理を加えるとこの問題をクリアできる

その際の共通処理は分けて考えるといいよね(privateで
```php
// 値受け渡し
$hoge->fuga = false;
// 呼び出し
$hoge = self::setDefaultValueForSave($hoge)

```
## 画像のアップロード
input type=fileだけでは送信できない。
enctype属性の関係上
```php:
{{ Form::open(['route' => ['hoge.store'], 'method' => 'post', 'files' => true]) }}
```
としないと送信されない（セキュリティ対策
http://www.htmq.com/html5/form.shtml

input=file のvalueにはold的な使い方で値をさしこめない。
>それができたら、ユーザーのPC上の任意のデータを勝手に集めることができてしまい、セキュリティ上の問題になってしまう
色々試したけど、現状updateとstoreのバリデーションを分離するしか解決方法はない
→余談だが、更新の場合は入り口を厳しくすればよくてバリデーションはゆるくていい

### Formが動かないor おかしいとき
・PostRequestの値はセットされているか
・ちゃんとfillやforeachなどして値をセットしたあとセーブしているか
・そもそもrequestとして送信される値は適切か

### 郵便番号　一発住所
https://github.com/yubinbango/yubinbango
## エラー
### An invalid form control with name='hoge' is not focusable.
いくつかパターンがあるが、htmlバリデーションでキレてる

classのrequiredを外せばいい（詳細は調べるとわかる