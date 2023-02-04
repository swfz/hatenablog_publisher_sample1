# Example

このリポジトリは[hatenablog_publisher](https://github.com/swfz/hatenablog_publisher)というGemのサンプルリポジトリです

このGemを使うと下記ができるようになります

- Markdown記事をコマンドラインからはてなブログへ投稿
- Markdown内に画像を扱っている場合はその画像をはてなフォトライフへアップロードし専用のリンクを作成した状態で投稿
    - はてなブログ上で画像が表示される
- 一度投稿した記事の更新
    - 記事の状態をファイルに保存

## install

```
gem install hatenablog_publisher
```

## 投稿の準備と設定
### 各種キーの取得

Gem内部で、はてなブログのAPIを叩くためにOAuth認証を使用します

下記ドキュメントをもとにconsumer_key, consumer_secret, access_token, access_token_secretを取得します

[Consumer key を取得して OAuth 開発をはじめよう | Hatena Developer Center](https://developer.hatena.ne.jp/ja/documents/auth/apis/oauth/consumer/)

### 認証に必要な設定

次のファイルを用意します

- hatenablog_publisher_config.yml

```
consumer_key: <%= ENV['HATENABLOG_CONSUMER_KEY'] %>
consumer_secret: <%= ENV['HATENABLOG_CONSUMER_SECRET'] %>
access_token: <%= ENV['HATENABLOG_ACCESS_TOKEN'] %>
access_token_secret: <%= ENV['HATENABLOG_ACCESS_TOKEN_SECRET'] %>
```

最低限各種キーを環境変数から読み込みのため上記の設定が必要です

### 投稿に必要な情報の設定

- hatenablog_publisher_config.yml

```
consumer_key: <%= ENV['HATENABLOG_CONSUMER_KEY'] %>
consumer_secret: <%= ENV['HATENABLOG_CONSUMER_SECRET'] %>
access_token: <%= ENV['HATENABLOG_ACCESS_TOKEN'] %>
access_token_secret: <%= ENV['HATENABLOG_ACCESS_TOKEN_SECRET'] %>
user: <user_name>
site: <site_url>
```

- user
    - 投稿するユーザー名、OAuthアクセストークンを取得したユーザー

- site
    - ブログのURL
    - 例) https://swfz-sample.hatenablog.jp/

user,siteは設定ファイルに記載せず、コマンドラインオプションで指定しても投稿可能です

## 記事の作成

次のようなMarkdownファイルを用意します

```
---
title: Sampleタイトル
category:
- Test
- サンプル
---

## サンプルの話

今日は〜〜でした

![text](sample01.png)
```

frontmatterでは次の項目を設定するとはてなブログに反映されます

- title
    - 記事のタイトル
- category
    - 記事のカテゴリ
    - 複数ある場合は複数指定

## 投稿
### 下書き用の投稿

```
hatenablog_publish --filename contents/sample.md --draft
```

- filename
    - 投稿対象のファイル名を指定します

- --draft
    - 下書きで投稿したい時はこのオプションを使用します

### 公開用の投稿

```
hatenablog_publish --filename contents/sample.md
```

### 投稿後の状態の保存

公開用、下書き用どちらの投稿でも投稿したら記事のIDが付与され、Markdownのfrontmatterに情報が追加されます

このIDをもとに記事の更新などを判断しているので消してしまうと再度新規の投稿として扱われてしまうので注意してください

その他画像もある場合は画像のID、URLなどもfrontmatterに追加されます

こちらも記事のIDと同様消してしまうと再度新規ではてなフォトライフに投稿するような仕様になっていますので気をつけてください

## オプションの一覧

| name      | 必須 | 引数 |概要                                        | 使用例                                     |
| :-        | :-   | :-   | :-                                          | :-                                        |
| user      | ○   | あり | 投稿ユーザー                               | --user swfz                                |
| site      | ○   | あり | ブログのURL                                | --site https://sample-swfz.hatenablog.jp/  |
| filename  | ○   | あり | 記事のファイルパス                         | --filename contents/sample.md              |
| draft     |      | なし | 下書き状態で投稿                           | --draft                                    |
| config    |      | あり | 設定ファイルのパス指定                    | --config custom_config_file.yml             |
| data_file |      | あり | 記事データの保存ファイル指定              | --data_file articles.yml                    |
| ad_type   |      | あり | 広告タイプ（現在Amazonのアソシエイトのみ）| --ad_type amazon                           |
| ad_file   |      | あり | 広告のデータファイル                       | --ad_file amazon_url_list.yml              |
| trace     |      | なし | デバッグモード                             | --trace                                    |


## Actionsから投稿する

contents以下のmarkdownファイルに変更があった場合に自動でデプロイできるようなActionsの設定サンプルを用意した

- pull requestで変更があった場合は下書き設定で投稿
- mainへの変更があった場合は公開設定で投稿

secrets, variablesに下記設定を登録する

### secrets
- HATENABLOG_ACCESS_TOKEN
- HATENABLOG_ACCESS_TOKEN_SECRET
- HATENABLOG_CONSUMER_KEY
- HATENABLOG_CONSUMER_SECRET

各種OAuthのキー

### variables
- PUBLISH_SITE
- PUBLISH_USER

オプション項目での`user`, `site`の値
