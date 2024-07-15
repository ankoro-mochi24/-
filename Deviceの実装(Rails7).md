# 導入手順
1、Gemfileに『gem 'devise'』を追加  
2、コマンドで  
└bundle install（dockerを使っている場合はdokcer compose run web bashのを先に実行）  
└rails g devise:install  
└rails g devise User  
2.5、nameカラムなどを追加したい場合は生成されたmigrationファイルに追記  
└rails db:migrate  
3、rails g devise:views  
 **※ログインに関するビューが自動生成されるが、form_forを使っているビューがあるので、form_withに変更したほうがよい(24/07/15現在)**

___

# よく使うコードなどまとめ
※CSRF攻撃対策（とりあえず書いといたほうがいい）
```
# app\controllers\application_controller.rb
  protect_from_forgery with: :exception # CSRF攻撃からアプリケーションを保護するためのメソッド
```
___
※カラム追加コード例(nameカラム)  
1、マイグレーションファイルに追加したいカラム情報を追記
```
# db\migrate\20yymmdd******_devise_create_users.rb
    create_table :users do |t|
    ...
      t.string :name, null: false, default: "" # nameカラムを追加
    end
```
2、applicationコントローラーにてdbへの保存を許可するコードを追記（**このように既述しないと、Deviceに自動で保存をはじかれてしまう**）
```
# app\controllers\application_controller.rb
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception # CSRF攻撃からアプリケーションを保護するためのメソッド

  before_action :configure_permitted_parameters, if: :devise_controller? #

  def configure_permitted_parameters #
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name]) # サインアップ時にnameパラメータを許可
    devise_parameter_sanitizer.permit(:account_update, keys: [:name]) # アカウント更新時にnameパラメータを許可
  end
end
```
3、app\views\devise\registrations\new.html.erbにnameの入力フォームを追加
```
#app\views\devise\registrations\new.html.erb
...
  <%= render "devise/shared/error_messages", resource: resource %>

  <div class="field">
    <%= f.label :name %><br />
    <%= f.text_field :name, autofocus: true, autocomplete: "name" %>
  </div>
...
```
4、modelにバリデーションを追加
```
#app\models\user.rb
  validates :name, presence: true
```
___
※ログインリンク/ログアウト/新規登録のテンプレート  
```
<% if user_signed_in? %>
  <%= button_to "ログアウト", destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to "ログイン", new_user_session_path %>
  <%= link_to "新規登録", new_user_registration_path %>
<% end %>
```
 **※ログアウトについてはlink_toを使うとrouting errorが発生するため注意(24/07/15現在)**
