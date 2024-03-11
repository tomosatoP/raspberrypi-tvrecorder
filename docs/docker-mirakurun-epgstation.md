# docker-mirakurun-epgstation 導入

https://github.com/l3tnun/docker-mirakurun-epgstation


- docker-mirakurun-epgstation manuals
  - [Readme.md](https://github.com/l3tnun/docker-mirakurun-epgstation/blob/v2/Readme.md)

- Mirakurun manuals
  - [setup by platform](https://github.com/Chinachu/Mirakurun/blob/master/doc/Platforms.md)
  - [configureation](https://github.com/Chinachu/Mirakurun/blob/master/doc/Configuration.md)
  - WebAPI => `http://<hostname or ip address>:<port>/api/debug`

- EPGStation manuals
  - [setup](https://github.com/l3tnun/EPGStation/blob/master/doc/linux-setup.md)
  - [config.yml](https://github.com/l3tnun/EPGStation/blob/master/doc/conf-manual.md)
  - [WebAPI](https://github.com/l3tnun/EPGStation/blob/master/doc/webapi.md)


## 導入

### ソースの取得

~~~sh
git clone --depth 1 https://github.com/l3tnun/docker-mirakurun-epgstation.git
~~~

### 設定ファイル (*.yml) の作成
~~~sh
cd docker-mirakurun-epgstation
cp docker-compose-sample.yml docker-compose.yml

# epgstation
cd epgstation/config/
cp enc.js.template enc.js
cp config.yml.template config.yml
cp operatorLogConfig.sample.yml operatorLogConfig.yml
cp epgUpdaterLogConfig.sample.yml epgUpdaterLogConfig.yml
cp serviceLogConfig.sample.yml serviceLogConfig.yml
~~~

### 設定ファイル (*.yml) のカスタマイズ

[docker-compose.yml](files/docker-mirakurun-epgstation/docker-compose.yml)<br>
[mirakurun/conf/sever.yml](files/docker-mirakurun-epgstation/mirakurun/conf/server.yml)<br>
[mirakurun/conf/tuners.yml](files/docker-mirakurun-epgstation/mirakurun/conf/tuners.yml)<br>
[epgstation/config/config.yml](files/docker-mirakurun-epgstation/epgstation/config/config.yml)

### 録画コマンドの導入
~~~sh
which recpt1
# > /usr/local/bin/recpt1

cd docker-mirakurun-epgstation

mkdir -p mirakurun/opt/bin
cd mirakurun/opt/bin

sudo cp /usr/local/bin/recpt1 .
sudo cp /usr/local/bin/recpt1ctl .
sudo cp /usr/local/bin/checksignal .
~~~

### 一時的な録画ファイルの保存フォルダ作成

~~~sh
cd docker-mirakurun-epgstation

mkdir recordedTmp
~~~

### チャンネルスキャン (省略可)

地上波のみ
~~~sh
cd docker-mirakurun-epgstation

sudo docker compose build --pull
sudo docker compose run --rm -e SETUP=true mirakurun
~~~

### 運用

> epgstation の build (主に ffmpeg) は時間がかかるので気長に待つ
~~~sh
cd docker-mirakurun-epgstation

sudo docker compose build --pull
sudo docker compose up -d
~~~

||ポート|
|---|---|
|mirakurun|40772|
|epgstation|8888<br>8889|

#### チャンネルスキャン (WebAPI - mirakurun)

- 上書き
- 全チャンネルを `Disable` に設定

地上波
~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=GR&refresh=true&setDisabledOnAdd=true"
~~~
BS波
~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=BS&refresh=true&setDisabledOnAdd=true"
~~~
CS波 : チャンネルスキャンはできるが、視聴できない
~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=CS&refresh=true&setDisabledOnAdd=true"
~~~

#### ログ確認
~~~sh
cd docker-mirakurun-epgstation

sudo docker compose logs
~~~

#### 停止及び破棄
~~~sh
cd docker-mirakurun-epgstation

sudo docker compose stop
sudo docker compose down --rmi all
sudo docker system prune
~~~
---