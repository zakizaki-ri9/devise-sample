
# 概要

gem**devise**を理解するためのrailsプロジェクト。  
deviseの簡単なログイン機能を実装し、  
deviseから提供される機能を網羅＆理解する。

# 参考サイト

- [Railsの認証Gem、Deviseとはなんなのか](http://astap.hatenablog.jp/entry/2017/04/16/221349)
- [【Rails備忘録】deviseまとめ](https://qiita.com/ShinyaKato/items/a098a741a142616a753e)

# devise系コマンドのメモ

## rails g devise:install
devise全般的な設定ファイルと、  
View（画面）で使用するlocale（メッセージリソース）ファイルが生成される。  

```bash:コマンド
      create  config/initializers/devise.rb
      create  config/locales/devise.en.yml
```

**config/initializers/devise.rb**
認証周りの設定値を記載するファイル。  
パスワードの長さ等を変更するならこのコードを変更する必要あり。

**config/locales/devise.en.yml**
deviseで実装された箇所に表示してくれるメッセージリソースファイル。
英語版。日本語化は別途対応する。

## rails g devise User → rails db:migrate

devise（認証周り）の機能がサポートされたモデル、  
それに紐づくマイグレーションが生成される。  

```bash:コマンド
      invoke  active_record
      create    db/migrate/20180621230953_devise_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml
      insert    app/models/user.rb
       route  devise_for :users
```

**db/migrate/20180621230953_devise_create_users.rb**
マイグレーションファイル。

**app/models/user.rb**
生成された際、以下のようなコードが実装されている。
```ruby
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
```
パスワードの暗号化、サインアップ（登録）処理などがサポートされている状態。  

メールによる登録確認（confirmable）や  
OmniAuth（Twitter等を利用した登録）サポート（omniauthable）を  
有効にしたい場合は本コードを編集する必要あり。  

**config/routes.rb**
`devise_for :users`が追加されている。
生成したモデル「User」のルーティングを行なってくれている状態。
ルーティングを独自にいじりたい場合は、以下記事参照。

- [devise でログインログアウトのパスを変更したいときの注意点](https://blog.willnet.in/entry/2013/07/02/230352)
