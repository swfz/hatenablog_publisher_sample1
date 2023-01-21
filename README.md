# Example

このリポジトリは[hatenablog_publisher](https://github.com/swfz/hatenablog_publisher) というGem使用例のサンプルリポジトリです

このGemを使うと下記ができるようになります

- Markdownで書いた記事をはてなブログへ投稿できる
- コマンドラインからはてなブログへ投稿
- Markdown内に画像を扱っている場合はその画像をはてなフォトライフへアップロードし専用のリンクを作成した状態で投稿します
- 記事の状態を持つため記事の更新などを行えます

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

- hatenablog_publisher_config.yml

```
consumer_key: <%= ENV['HATENABLOG_CONSUMER_KEY'] %>
consumer_secret: <%= ENV['HATENABLOG_CONSUMER_SECRET'] %>
access_token: <%= ENV['HATENABLOG_ACCESS_TOKEN'] %>
access_token_secret: <%= ENV['HATENABLOG_ACCESS_TOKEN_SECRET'] %>
```

最低限各種キーの読み込みのため上記の設定が必要です

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

user,siteに関しては設定ファイルに記載せず、コマンドラインオプションで指定しても投稿可能です


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

### 投稿後

公開用、下書き用どちらの投稿でも投稿したら記事のIDが付与され、Markdownのfrontmatterに情報が追加されます

このIDをもとに記事の更新などを判断しているので消してしまうと再度新規の投稿として扱われてしまうので注意してください

その他画像もある場合は画像のID、URLなどもfrontmatterに追加されます

こちらも記事のIDと同様消してしまうと再度新規でフォトライフに投稿するような仕様になっています

