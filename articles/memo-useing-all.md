---
title: "memo"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: false
---
docker-laravel-vue

npm installもdocker

npm run watch でエラー

→dockerで環境を作ってるんだから、docker-compose のコンテナで起動しないとエラーはく

docker-compose exec web npm install 

docker-compose exec web npm install --save vue-router

docker-compose exec web npm run watch

で解決