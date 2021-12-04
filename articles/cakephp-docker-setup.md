---
title: "CakePHPの開発環境をdocker+php+nginx+PostgreSQLで構築する"
emoji: "🐚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["php", "cakephp","docker", "nginx", "postgresql"]
published: true
---

### 前提
```
php-fpm: 7.4
CakePHP: 任意
Nginx: 1.19.x
PostgreSQL: 12.x
apt使用
```
### 構成
宗教上の理由でdocker関連ファイルを.docker配下に配置しています。
```php
.
├── .docker
│   ├── app
│   │   ├── Dockerfile
│   │   └── conf
│   │         ├── php.ini
│   │         └── docker.conf
│   ├── db
│   └── nginx
│       ├── Dockerfile
│       └── conf
│           └── default.conf
├── .git
├── .gitignore
├── README.md
└── docker-compose.yml
```

## docker環境の構築
### docker-compose.yml
```yml:docker-compose.yml
version: '3'
services:
  nginx:
    container_name: nginx
    build:
      context: .docker/nginx
      dockerfile: Dockerfile
    ports:
      - 80:80
    volumes:
      - .:/var/www/html
    tty: true
    depends_on:
      - app

  app:
    container_name: app
    build:
      context: .docker/app
      dockerfile: Dockerfile
    environment:
      LANG: 'ja_JP.UTF-8'
      TZ: 'Asia/Tokyo'
      APP_NAME: 'CakePHP'
      APP_ENV: 'development'
      APP_DEBUG: 'true'
      APP_URL: 'http://localhost'
      LOG_CHANNEL: 'stderr'
      DB_CONNECTION: 'pgsql'
      DB_HOST: 'db'
      DB_PORT: '5432'
      DB_DATABASE: 'cake_development'
      DB_DATABASE_TEST: 'cake_testing'
      DB_USERNAME: 'docker'
      DB_PASSWORD: 'docker'
    env_file:
      - .env
    volumes:
      - .:/var/www/html
    expose:
      - 9000
    tty: true
    depends_on:
      - db

  db:
    image: postgres:12-alpine
    container_name: db
    environment:
      TZ: 'Asia/Tokyo'
      POSTGRES_USER: 'docker'
      POSTGRES_ROOT_PASSWORD: 'docker'
      POSTGRES_PASSWORD: 'docker'
      POSTGRES_DB: 'cake_development'
    volumes:
      - ./.docker/db/data:/var/lib/postgresql/data
      - ./.docker/db/sql:/docker-entrypoint-initdb.d
    ports:
      - 5432:5432
```

### app
コメントアウトしている部分がcake導入に必要なものに該当。必ず入れてくださいね!
```Dockerfile:.docker/app/Dockerfile
FROM php:7.4-fpm

ENV TZ Asia/Tokyo
ENV COMPOSER_ALLOW_SUPERUSER 1

# install Lib for composer
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash - && \
    apt-get update -qq && \
    apt-get install -y libicu-dev &&\
    apt-get install --no-install-recommends -y libpq-dev libonig-dev libxml2-dev nodejs git zip unzip && \
    apt-get install --no-install-recommends -y zlib1g-dev libfreetype6-dev libpng-dev libjpeg62-turbo-dev libwebp-dev libxpm-dev && \
    apt-get clean && \
    rm -rf /var/cache/apt && \
    npm install npm@latest -g

# add extention
RUN docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp --with-xpm && \
    docker-php-ext-install -j$(nproc) gd && \
    docker-php-ext-install mbstring pdo pdo_pgsql json &&\
    docker-php-ext-install intl

# php.conf php-fpm.conf
COPY conf/php.ini /usr/local/etc/php/php.ini
COPY conf/docker.conf /usr/local/etc/php-fpm.d/docker.conf

# install Composer
RUN curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    chmod +x /usr/local/bin/composer

WORKDIR /app

# CakePHP に必要な拡張機能
# apt-get install -y libicu-dev &&\
# docker-php-ext-install intl \
``` 

php.iniやdocker.confはお好みで。

### nginx
```Dockerfile:.docker/nginx/Dockerfile
FROM nginx:1.19-alpine

ENV TZ Asia/Tokyo

COPY conf/default.conf /etc/nginx/conf.d/default.conf
```
```conf:default.conf
server {
    listen 80;

    # 指定先注意
    root  /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass   app:9000;
        fastcgi_index  index.php;

        include        fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param  PATH_INFO $fastcgi_path_info;
    }
}
```

ここまででbuild環境はok。以下コマンドで実行
buildにコケる場合、参照先やパッケージの入れ方などが悪いので見直してみてください><>
```
docker-compose build
docker-compose up
```

## setup
ここまででPHP, nginx, PostgreSQLの環境が整ったのでCakePHPをsetupしていきます!

1. CakePHPのinstall
```
// project 名sampleで作成(4.x)
docker-compose exec app composer create-project --prefer-dist cakephp/app sample

// versionを指定
docker-compose exec app composer create-project --prefer-dist cakephp/app:^4.0 {プロジェクト名}
```

ディレクトリを移す
```
cp -R sample/. ./ 
rm -rf sample/
```

http://localhost/ に一旦アクセスする
=> エラーが出ると思います。
```
Database driver Cake\Database\Driver\Mysql cannot be used due to 
a missing PHP extension or unmet dependency
```

2. driver設定の見直し
CakePHP3ではデフォルトで、mysql のドライバを参照する設定になっているので、参照先を変更します。
config/app.phpを見てみると以下のようになっている。
```php:config/app.php
'Datasources' => [
    /*
     * These configurations should contain permanent settings used
     * by all environments.
     *
     * The values in app_local.php will override any values set here
     * and should be used for local and per-environment configurations.
     *
     * Environment variable based configurations can be loaded here or
     * in app_local.php depending on the applications needs.
     */
    'default' => [
      'className' => Connection::class,
      'driver' => Mysql::class,
      'persistent' => false,
      'timezone' => 'UTC',
      // 略
    ],
    'test' => [
      'className' => Connection::class,
      'driver' => Mysql::class,
      // 略
    ],
```

したがってdriver設定を以下のように変える。
```
'driver' => \Cake\Database\Driver\Postgres::class,
```
なお、defaultだけでなくtest内のdriverも変更すること!

上記の流れで完了! welcome画面が確認できると思います。

http://localhost/ 

## あとがき
どちらかというと手順書のようになってしまいましたが、誰かの役に立てると幸いです。
途中説明を省略した部分もあるので、わかりにくい場合は以下を参考にしてください!

それでは失礼します><

https://github.com/Snails8/cakephp-api-sample

