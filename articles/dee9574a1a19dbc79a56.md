---
title: "bootstrap"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [bootstrap]
published: false
---
https://bootstrap-guide.com/components/carousel

グリットシステムcl-md-12
https://qiita.com/akatsuki174/items/53b7367b04ed0b066bbf
### container or container-fluid
Webサイトの大枠のこと
### div class="row"
グリッドシステム：container内のrow以下に記載できる

レイアウトを格子状に分解して配置でき、横幅を12分割可能
```
class = “col-{prefix}-{columns}”の形で書く(columnsは合計12になるように)
```
https://qiita.com/akatsuki174/items/53b7367b04ed0b066bbf
### card
cardは柔軟で拡張ができるコンテナのようなもの。
headers や footers のオプションなど色々作り込める。

(具体例は公式へ)[https://getbootstrap.jp/docs/4.2/components/card/]
### form
フォームを利用する場合の基本的な使い方としては、フォーム部品(input、textarea、select)にform-controlクラスを追加し、外側をform-groupクラスをつけたdivタグで囲む形になります。

https://qiita.com/zaburo/items/8983993d173c51cb3827