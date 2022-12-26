PhotoPrismをTraefikを用いてHTTPSで配信する。

前提条件:
* ドメインを所有していること(example.comとする)
* IPv4アドレスが割り当てられていること
* ポート番号80と443をポートフォワードする

手順:
* kp.example.com ==> example.com となるようなCNAMEレコードを作成する
* example.envを適切に書き換える
* 以下のコマンドを実行する

```sh
$ mv example.env .env
$ docker-compose up -d
```
