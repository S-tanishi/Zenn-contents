---
title: "middleware"
emoji: "🌊"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel]
published: false
---
## Middleware
ミドルウェアではユーザーからHttpリクエストに対して処理の前後に処理を挿入できる

User　→　route　（before middleware) →　controller →　view → response (After middleware) rendering → user

その前後の処理をそれぞれBefore MiddlewareとAfter Middlewareといい、レンダリングされるまでの入口と出口でミドルウェアを通ることになる。そのBeforeとAfterの書き分けはMiddlewareクラス内で行う

```
php artisan make:middleware {クラス名}
```
app/Http/Middleware

https://qiita.com/KZ-taran/items/a746a96b12489ae56553


Before MiddlewareもしくはAfter Middlewareの実装が完了したミドルウェアを実際に通すようにするにはKarnel.phpへ追記

https://readouble.com/laravel/5.5/ja/middleware.html

ミドルウェアの登録
ミドルウェアはKernel.phpで登録して初めて使えるようになる

ポイントは３つ
・全ての処理に共通して処理を行う場合ーー＄middleware 
・複数のミドルウェアをまとめて登録する場合ーー＄middleware Groupに登録
・単体で使うミドルウェアを登録ーー＄route　middlewareにキーと共に登録

https://laraweb.net/practice/1396/