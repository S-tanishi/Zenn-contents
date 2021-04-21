---
title: "[Laravel] Seeder Factory Migration Enum キホン"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [php, laravel]
published: true
---
## Seeder
```php:DatabaseSeeder.php
public function run()
{
    // 追記
    $this->call(TestUsersTableSeeder::class);
}
```
```php:SampleSeeder.php
public function run()
    {
        DB::table('users')->insert([
            [
                'name' => '鈴木 たにし',
                'kana' => 'スズキ タニシ',
                'email' => 'xxxxx@xxx.xx',
                'password' => bcrypt('1234'),
                'created_at' => Carbon::now(),
                'updated_at' => Carbon::now(),
            ],
            [
                'name' => '佐々木　たにし',
                'kana' => 'ササキ タニシ',
                'email' => 'xxxxx@xxxxx.xx',
                'password' => bcrypt('2345'),
                'created_at' => Carbon::now(),
                'updated_at' => Carbon::now(),
            ],
        ]);
    }
}
```

## iseed 
DBのデータをSeeder化
https://github.com/orangehill/iseed

### 使い方
公式見ればすぐに分かる。
```config/app.php
// provider内に
Orangehill\Iseed\IseedServiceProvider::class,
```

例えば、users_tableの取得をしたい時
```
docker-compose exec app php artisan iseed users
```
## 特徴
デメリット：上限に引っかかり大量のデータ取得とまでは行かない
https://github.com/orangehill/iseed/issues/4
ライブラリ見ればわかるけど、この問題（DB保存の話、仕組み）にぶち当たる
https://terasoluna-batch.github.io/guideline/current/ja/Ch05_Transaction.html

## Factory
```php:Sample.php
class Contact extends Model
{
    // 追記
    use HasFactory;
    // 以下略
```
```
docker-compose exec app php artisan make:factory SampleFactory
```
```php:SampleFactory.php
class UserFactory extends Factory
{
    protected $model = Sample::class;

    public function definition()
    {
        return [
            'name' => $this->faker->name,
            'kana' => $this->faker->firstName,
            'tel' => $this->faker->phoneNumber,
            'email' => $this->faker->unique()->safeEmail,
            'password' => bcrypt('test1234'), // password
            'sample_id' => collect([1, 2, 3])->random(),
        ];
    }
}
```

## migration 
### リレーション
```
1 : 1   hasOne      1 : n   hasMany      n:1     belongsTo        n : n   belongsToMany
```
- n:n の場合はそのまま表示するのが難しいので仮想的に1:n (or n:1)の関係を作る必要がある
→中間テーブルの作成
- work-staffのリレーションの場合work_staffでは通らない。staff_workで通る（ddで見ろ）

判断に迷う場合はその項目と相手に対し何本線を結ぶかが結ばれるか、で判定するとわかりやすい。
### リレーションテーブル命名ルール
- アルファベット順
- snake_caseで
- テーブル名の 単数形 を繋げる
### キーのもたせ方
・従テーブルで主テーブルを参照するために使われるキーを外部キーと呼ぶ。(1対n)
```php:create_hoge_images_table.php(1対N)
$table->bigInteger('hoge_id')->unsigned();
// 外部キー制約
$table->foreign('hoge_id')->references('id')->on('hoges')->onDelete('cascade');
```

・中間テーブルからみたら、N対1であるのでもたせ方は割愛
・中間テーブルのcreated_at updated_atタイムスタンプを自動的に保守
```php:Hoge.php(N対N model)
public function userInterests(): BelongsToMany
{
    return $this->belongsToMany('App\Models\UserInterest')->withTimestamps();
}
```
### リレーションデータの保存処理
- N対N　：saveのときにsyncメソッドをつける

## データのもたせ方　
### file で管理　（enum)
処理：データの数字だけもってきてfile内で展開する
### DB側でもたせる (model)
処理：DB側で全てインスタンス化した上で検索をかけ、持ってくる（DB内で展開
ORMはサーバー側で負担になり、最悪落ちる。
### 例
```php:
// APP側で処理
$hoges  = Work::query()->with('hogeImages')->get()->sortByDesc('id')->take(6);
// DB側
$hoges = Work::query()->with('hogeImages')->orderBy('id')->limit(6)->get();
```
orderBy　： SQl側で処理をする場合
sortBy　   ：collection側で処理をする場合
## Enum
https://github.com/BenSampo/laravel-enum
```
docker-compose exec app php artisan make:enum XXX
```
Facade内に記述することで、controllerで値を渡すことなく処理を通せる。
```
余談：Facade
・他の言語だとヘルパーに当たる。
・ファイル内に記述することで、classのimportができる。
・ファイルに干渉するので乱用要注意
```
### リレーション
例えばformで、複数選択可能のコンテンツがある時
Enum側から問い合わせることはない（=よってhasMany
### Model
```php:Interest.php
class Interest extends Model {
  protected $guarded = [];
}
```
書かないと以下のエラーはく
```
Add [interest] to fillable property to allow mass assignment on [App\Models\Interest]."
```
### リレーションデータの取り出し方
```
// Aなどはモデルのメソッド名に対応している
User::query->with(['A.B.C', 'A', 'B']);
```
A.B.C：```RailsCompany-- Rails-- station```のような３つに渡るデータを取得したいときに指定する