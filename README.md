
# Bedrock and Docker Compose Sample

## Fisrtly, run mysql container

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

1. `docker_build/docker-compose.yml`編集。`shared-volume` の `device` をhostのbedrockディレクトリに変更する（コメントアウトswitch）

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

2. ビルド・稼働させる

```sh
cd docker_build
docker-compose down -v --remove-orphans && docker-compose up --build
```

3. 開発
   - `bedrock` 配下を編集するとreloadで反映される
   - プラグイン等はcomposer installする