---
title: "Carbon"
emoji: "👌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: false
---
### A simple PHP API extension for DateTime.
[Carbon公式サイト](https://carbon.nesbot.com/)
[github](https://github.com/briannesbitt/carbon)

>Carbon(カーボン)はPHPに標準実装されているDateTimeクラスを継承したクラスで、Laravelなどのメジャーフレームワークでも採用されている日時を扱うクラスです。CarbonはDateTimeクラスの不便な部分を拡張しているので、時間の比較、時間の加算/減算をより便利に使うことができます。

CarbonとはPHPのDateTimeクラスをオーバーラップした日付操作ライブラリ

[使い方の具体例,参考](https://qiita.com/yudsuzuk/items/ff894bd0b76d4657741d)
### 基本
```php:
Carbon::now();  // 2020-11-01 09:34:53

Carbon::now()->startOfMonth();  // 2020-11-01 00:00:00

Carbon::now()->endOfMonth(); // 2020-11-31 23:59:59

Carbon::now()->startOfweek(); // 今週の月曜日
```
### 日付の取得
```php:
Carbon::now()->format('Ymd'); //20201101
### 日付の比較
```php:
<?php

require 'vendor/autoload.php';

use Carbon\Carbon;

$dt1 = new Carbon('2016-05-01');
$dt2 = new Carbon('2016-06-01');

// イコール
var_dump($dt1->eq($dt2)); // false

// より大きい　gt → greater than (不等号： $old > $latest)
var_dump($dt1->gt($dt2)); // false

// 以上  gte → greater than equal（不等号：$old >= $latest）
var_dump($dt1->gte($dt2)); // false

// より小さい lt →　less than　（不等号：$old < $latest）
var_dump($dt1->lt($dt2)); // true

// 以下　（不等号：$old <= $latest）
var_dump($dt1->lte($dt2)); // true

// 日付が２つの日付の間にあるか
var_dump(Carbon::parse('2016-05-15')->between($dt1, $dt2)); // true
```

[参考：Carbonで日付を比較する](https://qiita.com/ryoichi-u/items/e545c7f3d37445da463c)

[詳しい解説と具体例](https://technoledge.net/composer-carbon/)

https://uiuifree.com/blog/develop/php-date-carbon/

https://localdisk.hatenablog.com/entry/2014/01/30/便利な日時操作ライブラリ_Carbon