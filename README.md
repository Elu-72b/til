# TIL
使い方を探りながら草を生やしていきたい

---

# 11/13
### Rails基礎２（ヘッダー、フッターの実装）完了
### 躓きポイント
①：実装フェーズを完了したが`LoadError in StaticPagesController#top` のエラーが出た

②：①を解決したが`SassC::SyntaxError in StaticPages#top`のエラーが出た

③：フッターが表示されない

### ①
実装フェーズ（app/views/layouts/application.html.erb ファイルの編集）を完了したが
```
LoadError in StaticPagesController#top
cannot load such file -- sassc
Extracted source (around line #8):
```
のエラーが出た
原因：sassc gemが不足している →　Gemfileを確認
```
<略>
'# Use Sass to process CSS
'# gem "sassc-rails"
gem "dartsass-rails", "~> 0.4.0"
<略>
```
* 肝心のgem "sassc-rails"がコメントアウトしてる！！
→ '# 外してbundleをインストールすればOK

* bundleのインストール
docker-compose.ymlの中身からサービス名を確認
```
version: '3'
services:
  db:
<略>
  web: 　← Railsアプリケーションのサービス名は web
    build: .
<略>
```
→ Docker環境でbundle installを実行するコマンドは
　`docker compose run web bundle install`
　※`docker compose run` はコンテナ内でコマンドを実行するという意味で使用
 
### ②
①を解決したが
```SassC::SyntaxError in StaticPages#top
Showing /v3_basic_rails_basic/app/views/layouts/application.html.erb where line #8 raised:

Error: File to import not found or unreadable: bootstrap/dist/css/bootstrap.
        on line 1:1 of app/assets/stylesheets/application.bootstrap.scss
        from line 1:1 of app/assets/stylesheets/application.scss
```
のエラーが出た
エラーメッセージ：bootstrap/dist/css/bootstrapが見つからない
↑ これはBootstrapというCSSフレームワークに関連
→ Docker環境で、以下のコマンドを順番に実行
```
# 1. webコンテナに入る
docker compose exec web bash

# 2. JavaScriptパッケージをインストール
yarn install

# 3. 開発サーバーを起動
bin/dev
```

### ③
フッターが表示されない
app/views/layouts/application.html.erb 
```
<略>
  <body>
    <%= render 'shared/header' %>
    <%= yield %>
    <%= render 'shared/footer' %>　← 追加
  </body>
<略>
```
### →　LGTM

---
