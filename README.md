
# Bedrock and Docker Compose Sample

## 開発用setup手順

1. このrepositoryをclone
2. mysqldをなんらかの方法で立てる
   - [dockerで。](#run-mysql-container)
   - ホストでmysqldたててれば、コンテナからは`0.0.0.0`等でアクセスできるはず
3. [#development-build-開発用の編集可能ビルド](#development-build-開発用の編集可能ビルド)の手順でdocker-compose up
4. [Sage themeを使う場合](#sageのテンプレート開発)
   - このsetup完了後、ブラウザでwp管理画面のテーマ設定に行き、`Custom Sage`をActivateする


## Run mysql container

- `docker_mysql/docker-compose.yml` の `volumes` の `:` 左辺を自分のホストのmysqlデータディレクトリに設定する。あるいは `volumes` 設定自体を削除する（全てコンテナ内包）。あるいはお好みでmysql立てる
- mysqlコンテナを起動する。

```sh
cd docker_mysql
docker-compose up -d
```

- `docker_build/docker-compose.yml` の `networks.mysql.name` に上記で稼働したまたは別途設定しているmysqlへアクセスできるネットワークの設定を加える。（もとの `docker_mysql/docker-compose.yml` をそのまま起動してたら `mysql_default` のままで大丈夫なはず）

## Local build and run

- imageをビルドして稼働させる

```sh
cd docker_build
docker-compose down -v --remove-orphans && docker-compose up --build
```

## Build images

- imageをビルドする

```sh
cd docker_build
docker-compose down -v --remove-orphans && docker-compose build
```

## Development build 開発用の編集可能ビルド

1. `cp bedrock/.env.sample bedrock/.env`
   - 便宜調整
2. `docker_build/docker-compose.yml`編集。`shared-volume` の `device` をhostのbedrockディレクトリに変更する（コメントアウトswitch）

    ```yml
    ## Local Development
    volumes:
      shared-volume:
        driver_opts:
          type: none
          device: /path/to/bedrock
          o: bind
    ## Building Image for Deploy
    # volumes:
    #   shared-volume:
    ```

3. ビルド・稼働させる

    ```sh
    cd docker_build
    docker-compose down -v --remove-orphans && docker-compose up --build
    ```

4. 開発
   - `bedrock` 配下を編集するとreloadで反映される
   - プラグイン等はcomposer installする

### Sageのテンプレート開発

1. sageディレクトリ移動 `cd bedrock/web/app/themes/custom-sage`
2. `composer install`
3. `yarn`
4. `yarn build` 一度だけ実行すればおｋ
5. `yarn start` ... これで `http://localhost:3000` にアクセスするとテーマを編集しながら確認ができる
6. 本番用ビルド `yarn build:production` ... dockerfileで実行が必要

### php ビルトインサーバによる簡易serve

- docker稼働めんどいとき、自端末OSで直接phpサーバを稼働させる
- phpのextインストールなどが整ってないとあれこれエラーでる
- また、上記Sageテーマ編集との`yarn start`を組み合わせて実行することはできない

1. `bedrock/.env`編集

```sh
# dbをホストからアクセスできる値にし、wp_homeのポートを調整
DB_HOST=0.0.0.0
WP_HOME=http://localhost:8000
```

2. phpビルドインサーバ起動

```sh
php -S localhost:8000 -t bedrock/web
```
