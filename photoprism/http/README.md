HTTPでPhotoPrismを複数インスタンス実行する。

[2022-02-18当時のPhotoPrismのdocker-compose.yml](https://github.com/photoprism/photoprism/blob/4c1d68eb851676092751a922de2978b9ffe63f01/docker/examples/docker-compose.yml)を元にして、ポート番号とoriginalsディレクトリのパスだけ変更した。

実行するには、docker-compose.ymlがあるディレクトリそれぞれで`docker-compose up -d`とする。
