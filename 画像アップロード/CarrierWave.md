1、Gemfileにgem 'carrierwave'を追記  
2、bundleを実行  
3、rails generate uploader [クラス名]を実行(例：rails generate uploader Image) 下記のファイルが自動生成される  
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
4、
5、
6、
7、
8、
9、
10、
11、
