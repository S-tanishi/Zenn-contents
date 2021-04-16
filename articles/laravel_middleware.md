---
title: "[laravel 分離/DI/リポジトリパターン]"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: false
---
## laravel　分離
１　controller 　　　　　→　fatcontroller
２　model (public以降)　→  fatModel
３　リポジトリパターン（Serviceと別に

## リポジトリパターン
説明はデータの操作に関連するロジックを切り離し、別の抽象化したレイヤに任せることで
拡張性や保守性を高める。。といわれているが個人的には
開発工程や速度を短期的に大幅に短縮するものではなく、場合によってはマイナスになりうる可能性がある。
ただし、長期的に見たときにテストその他設計の関係上ある程度の規模以上なら最終コストは下がるという印象。

リポジトリにデータ操作を集中することで複雑になりすぎたテーブル更新やバグが発生しやすい処理など
分離、抽象化によってメリットを得ることを目的としている。
なので対象クラスが増加することに対してメリットのほうが多ければ採用すべき。

ただ、リポジトリに分けるだけではおそらく効果が薄くて
controller, service, repository, interfaceに分けたほうが進化を発揮すると
serviceに抽象化処理を書く
1対1対１ではない

app\provider\RepositoryProviderに記述

## DI:Dependency Injection
クラスのインスタンスを一括で済むようにしたもの Utility
laravel ではサービスコンテナと呼ぶ
実際のコーディングではサービスコンテナにインスタンス生成方法を結び付けて利用

処理の流れとしては
```
１）サービスコンテナにクラスを登録（バインド）

２）サービスコンテナからクラスを生成（リゾルブ）
```
つまり抽象的なことを行っている。
Utility　：あらゆるところで使う処理をまとめている（欄要注意）
facade登録すればbladeでも使える（＝　Helper）
## 流れ
フロントとバックを分離した場合で考えてみよう。よくある画像登録での動きを見てみる。
### Interface設計
クラスから"ユーザー定義型"の能力のみを分離した言語機構。PHPのそれはJavaのinterfaceのパクリ。
インスタンス化できないクラスの出来損ない
```php:HogeImageRepositoryInterface
interface HogeImageRepositoryInterface
{   
    public function getImages(int $hogeId): Collection;

    public function create(array $params): HogeImage;

    public function update(HogeImage $hogeImage, array $params): bool;
}
```
interfaceはそれ単独では役に立たない。もっぱら、タイプヒンティング（：型宣言）と組み合わせて使う

メリット：http://blog.tojiru.net/article/377526320.html
>タイプヒンティングでクラスではなくインターフェースを要求することで、
>謙虚で組み合わせやすいライブラリが作れて、しかも型による（ある程度の）品質保証ができます。
> 無闇にクラスを要求するのは不作法なのでやめましょう。

実装を変える場合、ORMで所得するパターン/クエリビルダで取得する
実装のパターンを変更する際に進化を発揮。

### Repository
データの取得のみ行うようにしている
```php:HogeImageRepository
```
### Service Provider
App/Providers/AppServiceProvider.phpにインターフェースと実装クラスを登録
```
public function register()
{
   // 略
   $this->app->bind('news_category', NewsCategoryService::class);
}   
```
### コントローラー
あとは引き出して渡すだけ
```
```

https://qiita.com/mikesorae/items/ff8192fb9cf106262dbf

