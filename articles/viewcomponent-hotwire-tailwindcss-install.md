---
title: 'Rails7 + ViewComponent + Hotwire + TailwindCSSを使ったアプリケーションの作成方法'
emoji: '🌊'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: []
published: false
---

# はじめに
# Rails7のインストールと設定

Docker環境で構築していきます。

下記のファイルを作成します。

```
./
  ├─ docker-compose.yml
  ├─ Dockerfile
  └─ Gemfile
```

```docker
FROM ruby:3.2.2

#===================
# packageセットアップ
#===================
# 最新版のyarnをリポジトリに反映
RUN wget --quiet -O - /tmp/pubkey.gpg https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

RUN apt-get update && apt-get install -y \
    yarn \
    nodejs \
    graphviz

RUN yarn global add n && n 18.4.0

#======================
# プロジェクトセットアップ
#======================
WORKDIR 'app'

# bundle
COPY ./Gemfile* ./
RUN bundle install

# node_modules
COPY ./package.json ./
COPY ./yarn.lock ./
RUN yarn
```

# ViewComponentの導入と使い方
# Hotwireの導入と使い方
# TailwindCSSのインストールと設定
# おわりに