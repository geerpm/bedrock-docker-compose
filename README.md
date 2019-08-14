
# Bedrock and Docker Compose Sample

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
