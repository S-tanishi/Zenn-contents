---
title: "model&migrationによるデータの扱い"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: true
---
https://readouble.com/laravel/5.5/ja/migrations.html
### 基本
```php:
$table->string('title');　//titleカラムのデータ型を 「string」 に設定
```
### modelとtableの関連付け
モデルとテーブルの関連付けについては、モデル内で実装する必要がある
テーブル名がモデルの複数形である場合、Laravel の方で自動的にモデルとテーブルを関連付ける。
### laravel Eloquent ORM
Laravel は Eloquent ORM があり、
クエリ及びクエリによって取得生成されたカラムをオブジェクト化したようなもの。

これを使用するためには、Eloquent Model の設定、外部キーを使うのであればモデル同士の関連付けが必要になる。

全てのEloquentモデルは Illuminate\Database\Eloquent\Model を継承する

```php:取得
$users = User::all(); //全モデルを取得
$user = User::find(1); // 主キーで１レコードを取得
```
```php:作成
User::create($data);
// または
public static function create(array $attributes = [])
{
    $model = new static($attributes);

    $model->save();

    return $model;
}
//　または
(new User($data))->save();
```
https://readouble.com/laravel/5.6/ja/eloquent.html¥

### migration

### データ型
```php:
・increments('id') //「符号なしINT」を使用した自動増分ID（主キー)
・string('カラム名', 長さ) 　//長さ指定のVARCHARカラム
・text('カラム名')  //TEXTカラム
・integer('カラム名') //数値データカラム
・timestamps() //created_atとupdate_atカラム
・date('カラム名') //日付カラム
・time('カラム名') //時間カラム
・dateTime(カラム名) //日時カラム
```
### 制約
カラムに UNIQUE 制約をつけることでカラムに重複した値を格納することができなくなる

### migrate:refresh
Migrationを再実行してテーブルを再構築。当然データも吹っ飛ぶ
### mysqlコマンド集
https://qiita.com/CyberMergina/items/f889519e6be19c46f5f4
クエリ
https://readouble.com/laravel/5.7/ja/queries.html
## エラー記録
### SQLSTATE[HY000] [2006] MySQL server has gone away (SQL: SHOW FULL TABLES WHERE table_type = 'BASE TABLE')
https://www.suzu6.net/posts/232-mysql-/

上記の記事通りなので引用させていただきます＞＜
>原因
>1 サーバータイムアウト
>2 接続をcloseしている
>3 クエリが大きすぎる or クエリの最大サイズの設定が小さい

dockerの場合、mysqlの中にあるmy.conf内に記載している
slow_query_logさんが悪さをしてるのかもね〜。

以下のコマンドでクエリの確認
```
>mysql> show variables like 'max_allowed_packet';
```

### SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client
 
.envやdatabase.phpの設定は大丈夫ですか？
それでもダメなら
caching_sha2_passwordを変更
```
mysql> alter user 'XXX'@'XXX' identified with mysql_native_password by 'XXX';
```
参考：https://qiita.com/ucan-lab/items/3ae911b7e13287a5b917

### SQLSTATE[42S01]: Base table or view already exists:
読んでそのまま、選択肢３つ

- そのテーブルだけをrollbackするか。php artisan migrate:rollback(一つ前の状態にロールバック)
- php artisan migrate:fresh(テーブルの中身も消えるけど）
- dbごと作り直す

### SQLSTATE[HY000] [1044] Access denied for user 'XXX'@'XXX' to database 'XXX'**
データベースを選択後、まずは権限とか確認したらどうですか？
```
mysql> show grants for 'ユーザー名'@'ホスト名'
```
必要に応じて権限を与えよう
```
mysql>GRANT 権限 ON レベル TO ユーザ IDENTIFIED BY PASSWORD '****'
```
https://www.dbonline.jp/mysql/user/index6.html

### SQLSTATE[42S01]: Base table or view already exists: 1050 Table 'XXX' already exists エラー

A テーブルを一度消して再合成すれば良い
```
php artisan migrate:fresh
```