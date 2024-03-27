# README

### Herokuへのデプロイ方法

1. heroku appの作成
```shell
$ heroku create
$ heroku heroku stack:set heroku-20
```

2. webpackのopenssl対応
  - see https://qiita.com/NaokiIshimura/items/550ca82e8e57aaea5582

```shell
heroku config:set NODE_OPTIONS='--openssl-legacy-provider' --app APP_NAME
```

3. buildpackの追加
- heroku appのsettingsからnodeとrubyのbuildpackを追加する
- node, rubyの順番にすること
- package.jsonに下記を追加
```json
  "engines": {
    "node": "20.x"
  }
```

4. データベースの作成
- heroku appのresourceからheroku postgresを追加する
- EssentialでOK
- database.ymlを変更
```yaml
production:
  <<: *default
  url: <%= ENV['DATABASE_URL'] %>
  adapter: postgresql
  database: db/production.postgresql
```

5. pumaの設定
- puma.rbを下記に変更
```ruby
max_threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }
min_threads_count = ENV.fetch("RAILS_MIN_THREADS") { max_threads_count }
threads min_threads_count, max_threads_count
port        ENV.fetch("PORT") { 3000 }
environment ENV.fetch("RAILS_ENV") { ENV['RACK_ENV'] || "production" }
pidfile ENV.fetch("PIDFILE") { "tmp/pids/server.pid" }
workers ENV.fetch("WEB_CONCURRENCY") { 2 }
preload_app!
plugin :tmp_restart
```
- ./Procfileを作成
```ruby
web: bundle exec puma -C config/puma.rb
```

6. (for Apple Silicon)
```shell
$ bundle lock --add-platform x86_64-linux
```

7. その他
- rails tutorial 7.5を参照