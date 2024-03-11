# Raspberry Pi

1. [Raspberry Pi と周辺機器](#raspberry-pi-と周辺機器)
1. [OS カスタマイズ - Raspberry Pi Imager -](#os-カスタマイズ---raspberry-pi-imager)
1. [System 設定](#system-設定)
   1. [Configuration](#configuration)
   1. [固定 IP アドレス設定](#固定-ip-アドレス設定)
1. [接続機器の確認](#接続機器の確認)
   1. [SCR3310-NTTcom](#scr3310-nttcom)
   1. [PLEX PX-Q3U4](#plex-px-q3u4)

## Raspberry Pi と周辺機器

|項目|内容|補足|
|---|---|---|
|本体|[Raspberry Pi 4 Model B 8GByte](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/)|ヒートシンクで冷却|
|Storage|[ESD-EC0120GBK](https://www.elecom.co.jp/products/ESD-EC0120GBK.html)||
|TV Tuner|[PLEX PX-Q3U4](http://www.plex-net.co.jp/product/px-q3u4/)|ファンを取り外し済み|
|ICカードリーダ|[SCR3310-NTTCom](https://www.ntt.com/business/services/application/authentication/jpki/download2.html)||
|OS|Raspberry Pi OS Lite (64bit)|bookworm|

## OS カスタマイズ - Raspberry Pi Imager -

|項目|適否|設定値1|設定値2|
|---|---|---|---|
|ホスト名|[X]|rpi4tvrec||
|ユーザー名とパスワードを設定する|[X]|play|******|
|Wi-Fiを設定する||||
|ロケール設定をする|[X]|Asia/Tokyo|jp|
|SSHを有効化する|[X]|パスワード認証を使う||

## System 設定

### [Configuration](https://www.raspberrypi.com/documentation/computers/configuration.html)

~~~sh
sudo apt update
sudo apt full-upgrade
sufo apt autoremove
#念の為に再起動
~~~
~~~sh
sudo raspi-config nonint do_boot_behaviour B2
sudo raspi-config nonint do_leds 0
sudo raspi-config nonint do_configure_keyboard jp
#念の為に再起動
~~~

### 固定 IP アドレス設定
デフォルトの有線接続設定の `Wired connection 1` を修正<br>
`ipv4.addresses` 等の値は、適宜に修正

~~~sh
nmcli -t device
# > eth0:ethernet:connected:Wired connection 1
# > lo:loopback:connected (externally):lo
# > wlan0:wifi:disconnected:
# > p2p-dev-wlan0:wifi-p2p:disconnected:

sudo nmcli connection modify "Wired connection 1" \
     ifname eth0 \
     type ethernet \
     autoconnect yes \
     ipv4.method manual \
     ipv4.addresses "192.168.68.135/24" \
     ipv4.gateway "192.168.68.1" \
     +ipv4.dns "192.168.68.1"

sudo nmcli connection reload
sudo nmcli connection up "Wired connection 1"
#念の為に再起動
~~~

## 接続機器の確認

### [SCR3310-NTTCom](https://www.ntt.com/business/services/application/authentication/jpki/download2.html) 

~~~sh
lsusb | grep SCM
# > Bus 003 Device 002: ID 04e6:511a SCM Microsystems, Inc.
~~~
この場合のデバイスファイルは、 `/dev/bus/usb/003/002`

### [PLEX PX-Q3U4](http://www.plex-net.co.jp/product/px-q3u4/)

~~~sh
lsusb | grep "N'Able"
# > Bus 001 Device 006: ID 0511:084a N'Able (DataBook) Technologies, Inc.
# > Bus 001 Device 005: ID 0511:084a N'Able (DataBook) Technologies, Inc.
~~~
チューナー 2 個が検出されている

---