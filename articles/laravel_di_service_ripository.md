---
title: "[Laravel] DI/リポジトリパターン"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [laravel, php]
published: true
---
## laravel　分離
- fatcontroller
- fatModel
- リポジトリパターン（Serviceと別に
## リポジトリパターン
説明はデータの操作に関連するロジックを切り離し、別の抽象化したレイヤに任せることで
拡張性や保守性を高める。。といわれているが所感としては

・開発工程や速度を短期的に大幅に短縮するものではない
・場合によってはマイナスになりうる可能性がある。
・長期的に見たときにテストその他設計の関係上ある程度の規模以上なら最終コストは下がる

という印象。

リポジトリにデータ操作を集中することで複雑になりすぎたテーブル更新やバグが発生しやすい処理など、分離、抽象化によってメリットを得ることを目的としている。
なのでクラスが増加することに対してメリットのほうが多ければ採用すべき。

>ただ、おそらくリポジトリに分けるだけでは効果が薄くて
controller, service, repository, interfaceに分けたほうが進化を発揮すると
そしてserviceに抽象化処理を書くこととテストなどの関係で長期的に見て恩恵がある思想だと私達のチームでは認識している。
当然、1対1対1対1ではない。
## DI:Dependency Injection
クラスのインスタンスを一括で済むようにしたもの。
laravel では**サービスコンテナ**と呼ぶ
サービスコンテナにインスタンス生成を結び付けて利用する。

ex)Utility
あらゆるところで使う処理をまとめている（欄要注意）
facade登録すればbladeでも使える（＝　Helper）

ただ会社によってはUtility禁止令など発令しているので考えて使うべき。
## 流れ
フロントとバックを分離した場合で考えてみる。
よくある画像登録でのざっくりとした動きを見てみる。
## Interface設計
・クラスから"ユーザー定義型"の能力のみを分離した言語機構。
・Javaのinterfaceのパクリのようなもの。
・インスタンス化できないクラスの出来損ない
```php:HogeImageRepositoryInterface
interface HogeImageRepositoryInterface
{   
    public function getImages(int $hogeId): Collection;

    public function create(array $params): HogeImage;
}
```
実装を変える場合、ORMで取得するパターン/クエリビルダで取得する
実装のパターンを変更する際に進化を発揮。
## サービスコンテナに登録（詳しくは下記)
```php:app/Providers/RepositoryServiceProvider.php
// 略(register内)
$this->app->bind(HogeImageRepositoryInterface::class, HogeImageRepository::class);
```
## Repository
値の取得のみ行うようにしている
```php:
/**
 * 画像取得
 * @param int $hogeId
 * @return Collection
 */
public function getImages(int $hogeId): Collection
{
    $hogeImages = HogeImage::query()->where('hoge_id', $hogeId)
        ->orderBy('id')->get();

    return $hogeImages;
}

/**
 * 画像登録
 * @param array $params
 * @return hogeImage
 */
public function create(array $params = []): hogeImage
{
    $hogeImage = new HogeImage();

    foreach ($params as $k => $v) {
        $hogeImage->$k = $v;
    }
    $hogeImage->save();

    return $hogeImage;
}
```
## Service Provider
サービスコンテナに入れ管理(自動で依存関係が解決)するために登録
```php:app/Providers/AppServiceProvider.php
public function register()
{
   // 略
   $this->app->bind('hogeImage', HogeImage::class);
}   
```
複雑な依存関係の場合エラーをこくが、シンプルなものに限って定義しなくても動いてしまう。
わからないのに動作する理由は以下のサイト様に詰まっています。

サービスコンテナについて非常にまとまっているので是非こちらも確認してみてください。
https://reffect.co.jp/laravel/laravel-service-container-understand
## Service
```php:
class hogeImageService
{
    protected $uploadTo = 'uploads/hoges';
    protected $hogeImageRepository;

    /**
     * hogeImageService constructor.
     * @param hogeImageRepository $hogeImageRepository
     */
    public function __construct(hogeImageRepository $hogeImageRepository)
    {
        $this->hogeImageRepository = $hogeImageRepository;
    }

    /**
     * 画像 一覧取得
     * @param int $hogeId
     * @return JsonResponse
     */
    public function getImages(int $hogeId): JsonResponse
    {
        $hoges = $this->hogeImageRepository->getImages($hogeId);

        return response()->json($hoges, 200);
    }

    /**
     * 画像 登録機能
     * @param Request $request
     * @return JsonResponse
     */
    public function create(Request $request): JsonResponse
    {
        $comment = $request['comment'];

        $params = [
            'hoge_id' => $hogeId,
            'comment' => $comment,
        ];

        $hogeImage = $this->hogeImageRepository->create($params);

        if ($request->hasFile('file')) {
            // 画像圧縮＆保存
            $file = $request->file('file');

            $fileName = $hogeId . '_'. $hogeImage->id . '.' . $file->getClientOriginalExtension();
            $path = Storage::disk('s3')->putFileAs($this->uploadTo, $file, $fileName,'public');

            $hogeImage->image = $path;
        }

        $hogeImage->save();
        $result = $hogeImage;
        return response()->json($result, 200);
    }
}
```
画像アップロードも同様な処理を頻繁に使うなら、Serviceに抽象化して
```php:
public function uploadFileToS3(int $id, string $uploadTo, Request $request): string
{
    $path = '';

    if ($request->hasFile('image')) {
        $file = $request->file('image');
        $fileName = $id . '.' . $file->getClientOriginalExtension();

        $path = Storage::disk('s3')->putFileAs($uploadTo, $file, $fileName, 'public');
    } else {
        $path = '';
    }

    return $path;
}    
```
以下のように取得してもいい。
```php
// protectedで宣言、constructでutilityなどサービスを読み込んだ上で
$path = $this->utility->uploadFileToS($id, $this->uploadTo, $request);
```
## コントローラー
コントローラーは値を渡すだけ
```php:
protected $hogeImageService;

public function __construct(HogeImageService $hogeImageService)
{
    $this->hogeImageService = $hogeImageService;
}

/**
 * 画像 一覧取得
 * @param $hogeId
 * @return JsonResponse
 */
public function getImages($hogeId): JsonResponse
{
    return $this->hogeImageService->getImages($hogeId);
}

/**
 * 画像 登録処理
 * @param Request $request
 * @return JsonResponse
 */
public function create(Request $request): JsonResponse
{
    return $this->hogeImageService->create($request);
}
```

## まとめ
正直このレベルだと負債にしかならないけど、
仮にほぼ同じ画像登録処理を使い回すのならservive全体を抽象化するなりすると
保守のコスト削減や可読性があがりますよね。エラー検証はログを確認すればいいし。

個人的にはサービスコンテナを理解していないと無駄なことだらけになりそうな気がします。
あとはメンバーの力量が違いすぎると逆にマイナスになる事例を聞いたりと。。

短期的な工数はかかりますが上回るメリットがあればガンガン書いていきたいところですね。