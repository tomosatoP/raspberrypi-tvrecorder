# TV 放送録画機

TV (地上波, BS) 放送を録画するシステムを構築

[docker-mirakurun-epgstation](https://github.com/l3tnun/docker-mirakurun-epgstation)

- Ansible を実行する為に、WSL を使用
- Hardware エンコードは、行わない

ハードウェア構成
1. [Raspberry Pi 4B 8GByte](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/)
    > ヒートシンクの自然放熱のみだが、安定動作している。
1. [ESD-EC0120GBKR](https://www.elecom.co.jp/products/ESD-EC0120GBKR.html)
    > 録画データは NAS に保存するので、uSD Card でも良かったかも？
1. [PLEX PX-Q3U4](http://www.plex-net.co.jp/product/px-q3u4/)
    > 風切り音が気になったのでファンを取り外したけど、2年以上も安定動作している。
1. [SCR3310-NTTCom](https://www.ntt.com/business/services/application/authentication/jpki/download2.html) 


## 構築手順

1. [Raspberry Pi の作業](RaspberryPi.md)
1. [WSL の作業](Ansible.md) -  Ansible を使います

## 運用

LAN で接続した PC 等から

||URL|できること|
|---|---|---|
|Mirakurun|http://\<hostname or ip address\>:40772/|チューナー/チャンネルの設定|
|EPGStation|http://\<hostname or ip address\>:8888/|番組表、予約、視聴、エンコード|
### 更新

`Raspberry Pi` から

~~~sh
cd docker-mirakurun-epgstation

sudo docker compose stop
sudo docker compose down --rmi all
# sudo docker container prune
# sudo docker system prune
git pull
sudo docker compose build --pull
sudo docker compose up -d
~~~
### 停止

`Raspberry Pi` から

~~~sh
cd docker-mirakurun-epgstation

sudo docker compose stop
~~~
### 破棄

~~~sh
cd docker-mirakurun-epgstation

sudo docker compose stop
sudo docker compose down --rmi all
# sudo docker container prune
# sudo docker system prune
~~~

## その他

### チャンネルスキャン 地上波

`Raspberry Pi` から

~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=GR&refresh=true&setDisabledOnAdd=true"
~~~

### チャンネルスキャン BS波

`Raspberry Pi` から

~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=BS&refresh=true&setDisabledOnAdd=true"
~~~

`Raspberry Pi` から

### チャンネルスキャン CS波

~~~sh
sudo curl -X PUT "http://localhost:40772/api/config/channels/scan?type=CS&refresh=true&setDisabledOnAdd=true"
~~~

### WebAPI

LAN で接続した PC 等から、チャンネルスキャンなど様々な操作が行える

||URL|
|---|---|
|Mirakurun|http://\<hostname or ip address\>:40772/api/debug/|
|EPGStation|http://\<hostname or ip address\>:8888/api/debug/|
---