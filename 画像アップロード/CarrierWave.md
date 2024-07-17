1、Gemfileにgem 'carrierwave'を追記  
2、bundleを実行  
3、rails generate uploader [画像用クラス名]を実行(例：rails generate uploader Image) 下記のファイルが自動生成される  
```
class ImageUploader < CarrierWave::Uploader::Base
  # コメントアウト
  storage :file
  # コメントアウト
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end
  # コメントアウト
end
```
4、画像を保存したいmodelファイルに下記いずれかを追記  
mount_uploader:1つのレコードに対して単一の画像を割り当てたい場合
mount_uploaders:1つのレコードに対して複数の画像を割り当てたい場合
```
# 例
# app\models\[modelクラス名].rb
class [modelクラス名] < ApplicationRecord
  mount_uploaders :[画像カラム名], [画像用クラス名]
```
mount_uploaderとmount_uploadersのテーブルイメージ  
【mount_uploaderの場合】
| id  | image          | created_at         | updated_at         |
|-----|-----------------|--------------------|--------------------|
| 1   | a.jpg | 2024-07-17 10:00:00 | 2024-07-17 10:00:00 |

【mount_uploadersの場合】
| id  | image          | created_at         | updated_at         |
|-----|-----------------|--------------------|--------------------|
| 1   | ["a.jpg", "b.jpg"] | 2024-07-17 10:00:00 | 2024-07-17 10:00:00 |

5、
6、
7、
8、
9、
10、
11、
