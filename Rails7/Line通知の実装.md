1. [LINE Notify](https://notify-bot.line.me/ja/)へアクセスし、ログイン
2. マイページに移動し、[トークンを発行する]をクリック
3. 個別トークンかグループトークンを選び、[発行する]をクリック
4. トークンが画面に表示されるのでコピー
5. 下記の通り必要なファイルの作成とコードを記述  

【.env】
```
LINE_NOTIFY_TOKEN=先ほどコピーしたトークン
```
【.gitignore】
```
.env
```
6.必要なgemの追加
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

.[LINE Developers]([https://developers.line.biz/ja/](https://account.line.biz/login?redirectUri=https%3A%2F%2Fdevelopers.line.biz%2Fconsole%2F))へアクセスし、ログイン
.![あ](https://i.gyazo.com/75a1bff3ff9604fccda7a1b1c27a6eeb.png)
.
.
