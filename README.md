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
