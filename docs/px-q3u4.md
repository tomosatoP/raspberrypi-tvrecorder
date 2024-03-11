# px-q3u4 用 ドライバ 導入

https://github.com/nns779/px4_drv

1. [関連パッケージを導入](#関連パッケージを導入)
1. [ソースを取得](#ソースを取得)
1. [firmware 抽出](#firmware-抽出)
1. [driver 導入](#driver-導入)
    1. [DKMS を使う場合](#dkms-を使う場合)
    1. [DKMS を使わない場合](#dkms-を使わない場合)
    1. [再起動後に確認](#再起動後に確認)
1. [参考：デバイスファイル](#参考デバイスファイル)
1. [未実施項目](#未実施項目)
    1. [各種設定ファイルの更新](#各種設定ファイルの更新)

## 関連パッケージを導入
- cmake
- autoconf
- automake
- ffmpeg
- unzip
- gcc
- make
- udev
- dkms
- g++
- git
- python3-dev
- python3-pip
- curl
- alpine

##  ソースを取得

~~~sh
git clone https://github.com/nns779/px4_drv.git
~~~

## firmware 抽出

~~~sh
cd px4_drv/fwtool

make
wget http://plex-net.co.jp/plex/pxq3u4/pxq3u4_BDA_ver1x64.zip -O pxq3u4_BDA_ver1x64.zip
unzip -oj pxq3u4_BDA_ver1x64.zip pxq3u4_BDA_ver1x64/PXQ3U4.sys
./fwtool PXQ3U4.sys it930x-firmware.bin
sudo mkdir -p /lib/firmware
sudo cp it930x-firmware.bin /lib/firmware
~~~

firmware を削除する場合に
~~~sh
sudo rm /lib/firmware/it930x-firmware.bin
~~~

## driver 導入

### DKMS を使う場合

- カーネルのソースとヘッダーがある場合に、DKMS を使った導入が可能
- カーネルの更新があった際に自動でこのドライバも更新されるので、便利

~~~sh
cd px4_drv
sudo cp -a ./ /usr/src/px4_drv-0.2.1
sudo dkms add px4_drv/0.2.1
sudo dkms install px4_drv/0.2.1
~~~

ドライバを削除する場合に

~~~sh
sudo dkms remove px4_drv/0.2.1 --all
sudo rm -rf /usr/src/px4_drv-0.2.1
~~~

### DKMS を使わない場合

- カーネルの更新の際に、ドライバの更新が必要

~~~sh
cd px4_drv/driver
make
sudo make install
~~~

ドライバを削除する場合に

~~~sh
cd px4_drv/driver
sudo make uninstall
~~~

### 再起動後に確認

~~~sh
lsmod | grep -e ^px4 
# px4_drv があればOK

ls /dev | grep -e ^px4
# px4video0 ~ 7 があればOK
~~~

## 参考：デバイスファイル

### BS・CS用チューナーデバイス
- /dev/px4video0
- /dev/px4video1
- /dev/px4video4
- /dev/px4video5

### 地上波用チューナーデバイス
- /dev/px4video2
- /dev/px4video3
- /dev/px4video6
- /dev/px4video7

---
## 未実施項目

### 各種設定ファイルの更新

~~~sh
sudo nano /etc/modules
~~~
~~~diff
+ px4_drv
~~~

~~~sh
sudo nano /boot/cmdlines.txt
~~~
~~~diff
+ coherent_pool=4M dwc_otg.host_rx_fifo_size=2048
~~~

~~~sh
sudo nano /etc/modprobe.d/px4_drv.conf
~~~
~~~diff
+ options px4_drv xfer_packets=51 urb_max_packets=816 max_urbs=6
~~~