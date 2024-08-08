1、Gemfileに必要なgemを記載しインストール
```
gem 'searchkick'               # 検索
gem 'elasticsearch', '~> 7.0'  # Elasticsearchとの連携
```
```
bundle install
```
2、docker-compose.ymlの編集
```
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.10.1
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"
```
```
    depends_on:
      - elasticsearch
```
3、Searchkickを使用するモデルの設定
```
# 例:Articleモデル
class Article < ApplicationRecord
  searchkick # Searchkickを使用するモデルに記述
end
```
4、Searchkickを使用するモデルのインデックスを作成
```
docker-compose run web rails searchkick:reindex
```
5、コントローラーとビューに検索機能を記述(下記は実装例)
```
# app/controllers/articles_controller.rb
class ArticlesController < ApplicationController
  def index
    if params[:query].present?
      @articles = Article.search(params[:query])
    else
      @articles = Article.all
    end
  end
end
```
```
<!-- app/views/articles/index.html.erb -->
<h1>Articles</h1>

<%= form_with url: articles_path, method: :get, local: true do |form| %>
  <%= form.label :query, "Search for Articles:" %>
  <%= form.text_field :query, params[:query] %>
  <%= form.submit "Search" %>
<% end %>

<ul>
  <% @articles.each do |article| %>
    <li><%= article.title %></li>
  <% end %>
</ul>
```
___
⓵search_dataによる検索対象の限定
```
# 例:Articleモデル
class Article < ApplicationRecord
  searchkick # Searchkickを使用するモデルに記述

  def search_data
    {
      title: title, # titleと
      body: body    # bodyだけを検索対象に指定
    }
  end
end
```
