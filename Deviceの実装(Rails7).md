1、Gemfileに『gem 'devise'』を追加  
2、コマンドで  
└bundle install（dockerを使っている場合はdokcer compose run web bashのを先に実行）  
└rails g devise:install  

└rails g devise User  
2.5、nameカラムなどを追加したい場合は生成されたmigrationファイルに追記  
'''
    create_table :users do |t|
    ...
      t.string :name, null: false, default: "" # nameカラムを追加
    end
'''
└rails db:migrate  
3、rails g devise:views  
