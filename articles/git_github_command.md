---
title: "git"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [git]
published: false
---
忘れがちなのでメモ
Git:分散型バーション管理システム

利点
・古いバージョンに戻せる
・ファイルを管理できる
・編集履歴を共有できる
・修正した部分を共有でき、統合することができる

ローカル→add→インデックス→commit→ローカルリポジトリ→push→リモートリポジトリ

https://tcd-theme.com/2019/12/what-is-git.html

### fetch marge pull
https://qiita.com/osamu1203/items/cb94ef9da02e1ec3e921

pull = fetch + merge origin/master

git fetch
fetchとはリモートリポジトリから最新情報をローカルリポジトリに持ってくるコマンド

fetchをしても、pullのようにファイルが更新されるわけではありません。
あくまでもローカルリポジトリが更新されるだけ

もっと詳しくいうと、例えばmasterブランチを使っているのであれば、
origin/masterが更新されるということ

fetchを行ったときに新しい更新があったとするとorigin/masterが最新になり、masterはその分の更新がまだ行われていない事になります。
そこでmergeが必要になる

git marge

git pull
上記の工程を一気に行うコマンド



【git】error: failed to push some refs to "URL"

https://qiita.com/chiaki-kjwr/items/118a5b3237c78d720582

原因
私がリモートでgithubのREADMEの変更を行っていたことが
今回のエラーの原因

「リモートのファイルがローカルのファイルも最新版だから、そのファイルにpushできないですよ

git fetch 

git margeで解決