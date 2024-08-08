※前提条件：dockerデスクトップの利用

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
      @articles = Article.search(params[:query]) # .serch = Searchkick gemのメソッド
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
⓶config\initializers\elasticsearch.rbで設定の一元管理
```
# config/initializers/elasticsearch.rb

Searchkick.client = Elasticsearch::Client.new(
  hosts: ["http://elasticsearch:9200"],  # Elasticsearchのホスト設定
  retry_on_failure: true,  # 失敗時のリトライを有効にする
  transport_options: { request: { timeout: 250 } }  # リクエストのタイムアウトを250秒に設定
)
```

※関連知識
1.Searchkick設定メソッド  
1.1. search  
最も基本的なメソッドで、検索を実行します。
```
# 全てのフィールドで検索
Article.search("keyword")

# 特定のフィールドで検索
Article.search("keyword", fields: [:title])
```
1.2. reindex  
データを再インデックスするために使用します。
```
# 単一のモデルを再インデックス
Article.reindex

# 全てのモデルを再インデックス
Searchkick.reindex_all
```
  
2. Searchkick設定オプション  
2.1. word_start
部分一致検索を可能にします。特にオートコンプリートに便利です。
```
class Article < ApplicationRecord
  searchkick word_start: [:title]
end

# 検索
Article.search("key", fields: [:title], match: :word_start)
```
2.2. suggest  
検索候補を提供するために使用します。
```
class Article < ApplicationRecord
  searchkick suggest: [:title]
end

# 検索
Article.search("titl", suggest: true).suggestions
```
2.3. highlight  
検索結果のハイライト表示を可能にします。
```
class Article < ApplicationRecord
  searchkick highlight: [:title, :body]
end

# 検索
results = Article.search("keyword", fields: [:title, :body], highlight: true)

results.each do |result|
  puts result.highlight["title"]
  puts result.highlight["body"]
end
```
3. その他の便利なメソッド  
3.1. similar  
類似したドキュメントを検索するために使用します。
```
article = Article.find(1)
similar_articles = article.similar(fields: [:title, :body])
```
3.2. searchkick_index  
カスタムインデックス名を使用する場合に便利です。
```
class Article < ApplicationRecord
  searchkick index_name: "custom_articles_index"
end
```
4. クエリの詳細設定  
4.1. boost  
特定のフィールドの重要度を高めるために使用します。
```
Article.search("keyword", fields: [{title: :boost}], boost_by: [:popularity])
```
4.2. where
特定の条件で絞り込み検索を行うために使用します。
```
Article.search("keyword", where: {published: true})
```


※関連gem
```
gem 'elasticsearch-model'
gem 'elasticsearch-rails'
```
