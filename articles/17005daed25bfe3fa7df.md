---
title: "laravel8系でclassがあるのにerorr class 'xxx' not found"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: false
---
import等問題なくclassはソースにあるのになぜ〜か読み込まない人向け

### 原因
Laravelではcomposerによるオートロード(Autoloader)の仕組みを利用しており、composerでクラス管理を行っている。

クラス名を急に変えたときなどcomposerの管轄外でクラス名等を変更するとおこるエラー。
つまりcomposer君が読み込んでないのです。

```
composer dump-autoload
```
で解決できる
### 背景
前述したとおりcomposerによるオートロード(Autoloader)の仕組みを利用しており、composerでクラス管理を行っている。
PHPによるオートロード機能とはファイルを自動で読み込む仕組みの事。

この機能があるからPHPファイルの冒頭に require を書く必要はなくなったらしい。
vendor/autoload.phpがオートロードの実体ファイル。

なおcomposer.jsonでオートロードするファイルを追加することもできる。

詳しい記述方法を以下
https://laraweb.net/surrounding/1642/