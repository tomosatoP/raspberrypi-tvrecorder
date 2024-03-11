# Raspberry Pi の初期設定

1. [設定](#設定)
    * Update system
    * ネットワーク接続を待ってからブートする化
    * 日本語化
    * 無効化：Bluetooth & WiFi
    * cgroup v2 制御有効化 : CPU & Memory
1. [SCR3310-NTTCom](#scr3310-nttcom)
1. [PLEX PX-Q3U4](#plex-px-q3u4)
1. [IP アドレス固定化](#固定-ip-アドレス化)
1. [未実施項目](#未実施項目)
    1. [SCR3310-NTTcom ドライバのインストール](#scr3310-nttcom-ドライバのインストール)

## 設定

~~~sh
sudo apt update
sudo apt full-upgrade
sufo apt autoremove
#念の為に再起動
~~~

~~~sh
sudo raspi-config nonint do_boot_behaviour B2
sudo raspi-config nonint do_leds 0
sudo raspi-config nonint do_boot_wait 1
sudo raspi-config nonint do_configure_keyboard jp
sudo raspi-config nonint do_change_locale ja_JP.UTF-8 UTF-8
sudo raspi-config nonint do_change_timezone Asia/Tokyo
#念の為に再起動
~~~
~~~sh
sudo nano /boot/firmware/config.txt
~~~
~~~diff
[all]
+ dtoverlay=disable-bt
+ dtoverlay=disable-wifi
+ dtparam=pwr_led_trigger=heartbeat
~~~
~~~sh
sudo nano /boot/firmware/cmdline.txt
~~~
~~~txt
# 1行になるように、行末に、スペースを挿入して追記
cgroup_enable=cpuset
cgroup_enable=memory
cgroup_memory=1
swapaccount=1
~~~


## [SCR3310-NTTCom](https://www.ntt.com/business/services/application/authentication/jpki/download2.html)

デバイスファイルを確認<br>
以下の場合、 `/dev/bus/usb/001/003`
~~~sh
lsusb | grep SCM
# > Bus 001 Device 003: ID 04e6:511a SCM Microsystems, Inc.
~~~

## [PLEX PX-Q3U4](http://www.plex-net.co.jp/product/px-q3u4/)

~~~sh
lsusb | grep "N'Able"
# > Bus 001 Device 006: ID 0511:084a N'Able (DataBook) Technologies, Inc.
# > Bus 001 Device 005: ID 0511:084a N'Able (DataBook) Technologies, Inc.
~~~
チューナー 2 個が検出されている

## 固定 IP アドレス化

省略

---
## 未実施項目

### SCR3310-NTTCom ドライバのインストール

docker版mirakurunに pcsc群 が含まれる為
- pscsd
- pcsc-tools
- libccid
- libpcsclite-dev 

動作チェック

~~~sh
pcsc_scan
# > Japanese Chijou Digital B-CAS (pay TV)
# Ctrl+C で停止
~~~

---