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

**※SQLiteを使用する場合はモデルファイルに『serialize :avatars, JSON』と記述が必要**  
5、画像のアップロードフォームを用意  
【mount_uploaderの場合】
```
# ビューファイル
<%= form_with model: [@変数名], local: true, html: { multipart: true } do |form| %>
  <p>
    <%= form.label :[カラム名], "画像" %>
    <%= form.file_field :[カラム名] %>
  </p>
  <p>
    <%= form.submit "Save" %>
  </p>
<% end %>

# コントローラー
# ストロングパラメーター
params.require(:[ルートキー]).permit(:[カラム名1], :[カラム名2], :[画像カラム名])
```
【mount_uploadersの場合】
```
# ビューファイル
# <%= form.file_field :[カラム名], multiple: true %>と記述が必要
<%= form_with model: [@変数名], local: true, html: { multipart: true } do |form| %>
  <p>
    <%= form.label :[カラム名], "画像" %>
    <%= form.file_field :[カラム名], multiple: true %>
  </p>
  <p>
    <%= form.submit "Save" %>
  </p>
<% end %>

# コントローラー
# ストロングパラメーター
params.require(:[ルートキー]).permit(:[カラム名1], :[カラム名2], {[画像カラム名]: []})
```

___
# 必ずしておいたほうがいい設定  
```
class ImageUploader < CarrierWave::Uploader::Base
  before :remove, :delete_empty_upstream_dirs
  storage :file

  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  #ファイルサイズ制限による不正アップロードの防止
  def size_range
    1..5.megabytes
  end

  # 拡張子の限定によるセキュリティ対策
  def extension_allowlist
    %w(jpg jpeg gif png)
  end

  # 画像へのアクセスをアップロード者と管理者に制限
  def set_permissions
    self.permissions = 0600
    self.directory_permissions = 0700
  end

  # ファイルの衝突回避
  # 一意のファイル名を生成する
  def filename
    "#{secure_token}.#{file.extension}"
  end

  # ファイルが空のディレクトリを自動削除
  def delete_empty_upstream_dirs
    path = ::File.expand_path(store_dir, root)
    Dir.delete(path) if Dir.exist?(path) && Dir.empty?(path) # 空のディレクトリが存在する場合のみ削除
  rescue SystemCallError
    true
  end

  protected

  # 一意のトークンを生成するメソッド
  def secure_token
    model.instance_variable_get("@#{mounted_as}_secure_token") || model.instance_variable_set("@#{mounted_as}_secure_token", SecureRandom.uuid)
  end

end
```
