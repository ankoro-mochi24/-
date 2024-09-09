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
.新規プロバイダー作成をクリック。任意のプロバイダー名を入力し、作成をクリック。  
![image](https://i.gyazo.com/75a1bff3ff9604fccda7a1b1c27a6eeb.png)  
![image](https://github.com/user-attachments/assets/5c320367-8a43-4c0b-b0fa-ebc38faf296e)  
.チャネル設定タブでLineログインをクリック。必要項目を記述し、作成。  
![image](https://i.gyazo.com/3e634d879cc1c00ea1307eac7f109ea6.png)  
.作成したLineログインチャネルをクリック。「チャネル基本設定」タブの中に「チャネルシークレット」という項目があるのでコピー  
※注意※発行ボタンを押すと、違うチャネルシークレットが発行される。変更したい場合は押せばいいが、不要なら押す必要はない。  
![image](https://i.gyazo.com/3aa839a50f356dfecf8fa6af6f70b41f.png)  
.  
![image](https://i.gyazo.com/fb78a5f7ff24ec10b70d6aae6717fef4.png)  
