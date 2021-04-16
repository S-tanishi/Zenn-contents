---
title: "[Laravel] 分離/DI/リポジトリパターン"
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
拡張性や保守性を高める。。といわれているが所感としては
開発工程や速度を短期的に大幅に短縮するものではなく、場合によってはマイナスになりうる可能性がある。
ただし、長期的に見たときにテストその他設計の関係上ある程度の規模以上なら最終コストは下がるという印象。

リポジトリにデータ操作を集中することで複雑になりすぎたテーブル更新やバグが発生しやすい処理など
分離、抽象化によってメリットを得ることを目的としている。
なので対象クラスが増加することに対してメリットのほうが多ければ採用すべき。

>ただ、おそらくリポジトリに分けるだけでは効果が薄くて
controller, service, repository, interfaceに分けたほうが進化を発揮すると
そしてserviceに抽象化処理を書くことで長期的に見て恩恵がある思想だと私達のチームでは認識している。
当然、1対1対1対1ではない

app\provider\RepositoryProviderに記述

## DI:Dependency Injection
クラスのインスタンスを一括で済むようにしたもの。
laravel ではサービスコンテナと呼ぶ
実際のコーディングではサービスコンテナにインスタンス生成方法を結び付けて利用

つまり抽象的なことを行っている。
ex)Utility　：あらゆるところで使う処理をまとめている（欄要注意）
facade登録すればbladeでも使える（＝　Helper）

ただ会社によってはUtility禁止令など発令しているので考えて使うべき。
## 流れ
フロントとバックを分離した場合で考えてみる。よくある画像登録での動きを見てみる。

説明のためバック側の処理(一覧と作成)しか記述していないけど、温度感として。
### Interface設計
クラスから"ユーザー定義型"の能力のみを分離した言語機構。PHPのそれはJavaのinterfaceのパクリ。
インスタンス化できないクラスの出来損ない
```php:HogeImageRepositoryInterface
interface HogeImageRepositoryInterface
{   
    public function getImages(int $hogeId): Collection;

    public function create(array $params): HogeImage;

}
```
実装を変える場合、ORMで所得するパターン/クエリビルダで取得する
実装のパターンを変更する際に進化を発揮。

### Repository
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
### Service
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
### コントローラー
あとは引き出して渡すだけ
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
https://qiita.com/mikesorae/items/ff8192fb9cf106262dbf

### Service Provider
App/Providers/AppServiceProvider.phpにインターフェースと実装クラスを登録
```
public function register()
{
   // 略
   $this->app->bind('news_category', NewsCategoryService::class);
}   
```
