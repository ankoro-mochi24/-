1、Gemfileに『gem 'devise'』を追加  
2、コマンドで  
└bundle install（dockerを使っている場合はdokcer compose run web bashのを先に実行）  
└rails g devise:install  
└rails g devise User  
2.5、nameカラムなどを追加したい場合は生成されたmigrationファイルに追記  
└rails db:migrate  
3、rails g devise:views  

※カラム追加コード例(nameカラム)  
```
    create_table :users do |t|
    ...
      t.string :name, null: false, default: "" # nameカラムを追加
    end
```
```
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  before_action :configure_permitted_parameters, if: :devise_controller?

  def configure_permitted_parameters
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name]) # 新規登録時(sign_up時)にnameというキーのパラメーターを追加で許可する
  end
end
```


※ログインリンク/ログアウト/新規登録のテンプレート  
```
<% if user_signed_in? %>
  <%= button_to "ログアウト", destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to "ログイン", new_user_session_path %>
  <%= link_to "新規登録", new_user_registration_path %>
<% end %>
```
**ログアウトについてはlink_toを使うとrouting errorが発生するため注意(24/07/15現在)**
