---
title: "[laravel] Seeder Factory"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: true
---
## Seeder
### sample
```php:DatabaseSeeder
public function run()
{
    // 追記
    $this->call(TestUsersTableSeeder::class);
}
```
```php:SampleSeeder
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

## factory
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