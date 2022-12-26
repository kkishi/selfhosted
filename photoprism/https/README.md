PhotoPrismを複数インスタンス実行し、Traefikを用いてHTTPSで配信する。

[単一インスタンスの場合](../https-single/README.md)との違い:
* Traefikは[独立したdocker-compose.ymlで](../../traefik/)実行しておく
* labelsやnetworksを適切に設定することで、Traefikからサービスがアクセスできるようにする

手順(それぞれのインスタンスについて):
* 使用するホスト名のCNAMEレコードを作成する
* example.envを適切に書き換える
* 以下のコマンドを実行する

```sh
$ mv example.env .env
$ docker-compose up -d
```

