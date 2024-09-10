### 目次：  
・必要なファイルを作成＆記述
・LINE_NOTIFY_TOKENの発行  
・LINE_NOTIFY_CLIENT_SECRETのコピペ  
・LINE_NOTIFY_REDIRECT_URIの設定  
・LINE_NOTIFY_CLIENT_IDの発行とコピペ  

# 必要なファイルを作成＆記述
【.env】
```
# 一旦空白でOKです。後述する手順の中で追記していくことになります。
```
【.gitignore】
```
.env
```
【GEMFILE】
```
gem 'httparty'

group :development, :test do
  gem 'dotenv-rails'
  gem "debug", platforms: %i[ mri mingw x64_mingw ]
end
```
【コマンド】
```
docker compose run web bundle
```
# LINE_NOTIFY_TOKENの発行
1. [LINE Notify](https://notify-bot.line.me/ja/)へアクセスし、ログイン
2. マイページに移動し、[トークンを発行する]をクリック
3. 個別トークンかグループトークンを選び、[発行する]をクリック
4. トークンが画面に表示されるのでコピー
5. .envに追記  
【.env】
```
LINE_NOTIFY_TOKEN=発行したトークンを貼付
```
# LINE_NOTIFY_CLIENT_SECRETのコピペ
