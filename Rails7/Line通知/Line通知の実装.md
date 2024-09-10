### 目次：  
・必要なファイルを作成＆記述  
・LINE Notifyにサービスの登録  
・【.env】の編集  

# 必要なファイルを作成＆記述
【.env】
```
LINE_NOTIFY_CLIENT_ID=
LINE_NOTIFY_CLIENT_SECRET=
LINE_NOTIFY_REDIRECT_URI=
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
# LINE Notifyにサービスの登録
1.[ngrok](https://dashboard.ngrok.com/get-started/setup/windows)にサインアップし、「Download for Windows (64-Bit)」をダウンロード  
※windowsセキュリティに引っかかる可能性があるので、その場合はリアルタイム保護を一時的に停止すれば解決する！必ず作業終了後に停止を解除すること！！  
![image](https://github.com/user-attachments/assets/a3e7cf70-e3a0-4b24-8e41-a8b012db8c65)

2.[ngrokのトークン生成リンク](https://dashboard.ngrok.com/get-started/your-authtoken)にアクセスし、トークンをコピー  
![image](https://github.com/user-attachments/assets/1ec1961f-8557-42a1-a3de-789ab70fc93c)

3.先ほどダウンロードしたngrok-v3-stable-windows-amd64.zipを展開し、ngrok.exeを起動。下記のコマンドを実行
![image](https://i.gyazo.com/052a3b6569c75a0c6926e5f3b5496797.png)
4.下記のコマンドを実行
```
ngrok config add-authtoken 発行したトークン
ngrok http 3000
```
5.実行結果の「Forwarding」に表示される「https://...ngrok-free.app」の部分をコピー
![image](https://i.gyazo.com/ff760a93812f5ccff333c7f4cde832d7.png)

6.[LINE Notifyのサービス登録画面](https://notify-bot.line.me/my/services/new)を開き、コピーしたURLを「サービスURL」の欄に貼り付け。コールバックURLは「http://localhost:3000/line_notify/callback」などとする。そのほかの項目も埋めていき、同意して次に進む  
※LINE Notifyのサービス登録画面の開き方：[LINE Notify](https://notify-bot.line.me/ja/)⇒[登録サービス管理](https://notify-bot.line.me/my/services/)⇒[サービスを登録する](https://notify-bot.line.me/my/services/new)
![image](https://github.com/user-attachments/assets/21f66b9d-abdd-41ef-b163-3b857d8d672b)

7.登録すると登録したメールアドレスに認証メールが届くので、認証を忘れないこと
![image](https://github.com/user-attachments/assets/f79abcb1-3dc8-4e56-b722-ff5804fbb892)

※※この公開URLは一時的なもので、ngrokのセッションが終了するとURLが無効になるため、ngrokを再度実行する際は、新しいURLをLINE Notifyに更新する必要があります。※※  
└ngrokのターミナルウィンドウを閉じる  
└Ctrl + Cを押す  
└コンピュータを再起動する  
※※リアルタイム保護を一時的に停止していた場合は必ず作業終了後に停止を解除すること！！※※

# 【.env】の編集  
1.登録したサービスを開く  
![image](https://github.com/user-attachments/assets/e7711fbb-0aa9-4952-918e-4bc3282d84d6)
2.サービスのページ上から[CLIENT_ID]、[CLIENT_SECRET]、[REDIRECT_URI]を確認し、【.env】に転記
![image](https://i.gyazo.com/4060fd34964a1025f34457487872bcf0.png)
![image](https://i.gyazo.com/4853fc43ced4ab21e291587839af0749.png)
