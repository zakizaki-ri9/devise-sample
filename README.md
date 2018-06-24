
# 概要

gem**devise**を理解するためのrailsプロジェクト。  
deviseの簡単なログイン機能を実装し、  
deviseから提供される機能を網羅＆理解する。

# 参考サイト

- [Railsの認証Gem、Deviseとはなんなのか](http://astap.hatenablog.jp/entry/2017/04/16/221349)
- [【Rails備忘録】deviseまとめ](https://qiita.com/ShinyaKato/items/a098a741a142616a753e)

# devise系コマンドのメモ

*devise*を用いたコマンドの意味合いおよび、  
生成される主要のファイルについての説明を記載する。

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

## rails g devise:views User

指定したモデル名「User」を元に  
サインイン・ログイン・パスワード変更・etc...  
のページを自動生成してくれる。　　

```bash
      invoke  Devise::Generators::SharedViewsGenerator
      create    app/views/users/shared
      create    app/views/users/shared/_links.html.erb
      invoke  form_for
      create    app/views/users/confirmations
      create    app/views/users/confirmations/new.html.erb
      create    app/views/users/passwords
      create    app/views/users/passwords/edit.html.erb
      create    app/views/users/passwords/new.html.erb
      create    app/views/users/registrations
      create    app/views/users/registrations/edit.html.erb
      create    app/views/users/registrations/new.html.erb
      create    app/views/users/sessions
      create    app/views/users/sessions/new.html.erb
      create    app/views/users/unlocks
      create    app/views/users/unlocks/new.html.erb
      invoke  erb
      create    app/views/users/mailer
      create    app/views/users/mailer/confirmation_instructions.html.erb
      create    app/views/users/mailer/email_changed.html.erb
      create    app/views/users/mailer/password_change.html.erb
      create    app/views/users/mailer/reset_password_instructions.html.erb
      create    app/views/users/mailer/unlock_instructions.html.erb
```

**app/views/users/shared/_links.html.erb**  
各　`〜.new.erb` の画面下部で部分ビューとして使用されている、  
サインイン・ログインページへのリンクを羅列したView。  

ソースを見てみると、  

```
<%- if devise_mapping.omniauthable? %>
  <%- resource_class.omniauth_providers.each do |provider| %>
    <%= link_to "Sign in with #{OmniAuth::Utils.camelize(provider)}", omniauth_authorize_path(resource_name, provider) %><br />
  <% end -%>
<% end -%>
```

すでに`OmniAuth`との連携が前提で実装されていることが伺える。  

## rails g devise:controllers User

指定したモデル名「User」を元にコントローラーを生成してくれる。  

```bash
      create  app/controllers/User/confirmations_controller.rb
      create  app/controllers/User/passwords_controller.rb
      create  app/controllers/User/registrations_controller.rb
      create  app/controllers/User/sessions_controller.rb
      create  app/controllers/User/unlocks_controller.rb
      create  app/controllers/User/omniauth_callbacks_controller.rb
===============================================================================

Some setup you must do manually if you haven't yet:

  Ensure you have overridden routes for generated controllers in your routes.rb.
  For example:

    Rails.application.routes.draw do
      devise_for :users, controllers: {
        sessions: 'users/sessions'
      }
    end

===============================================================================
```

コマンド入力後に出力されるログに記載されている通り、  
`config/routes.rb`に対して、  
自分でルーティングの設定を行う必要がある。  
