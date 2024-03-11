# 録画コマンド (recpt1) 導入

非 DVB チューナーの px-q3u4 用に録画コマンドを構築
> px-q3u4 は、ISDB-T/S チューナーを搭載

1. [docker 版 mirakurun の導入時にコンテナ内に導入](#docker-版-mirakurun-の導入時にコンテナ内に導入)
1. [未実施項目](#未実施項目)
    1. [録画コマンド recpt1 の構築](#録画コマンド-recpt1-の構築)

## docker 版 mirakurun の導入時にコンテナ内に導入

https://github.com/Chinachu/Mirakurun/blob/master/doc/Platforms.md#-how-to-use-non-dvb-devices

~~~sh
mkdir -p docker-mirakurun-epgstation/mirakurun/opt/bin
cd docker-mirakurun-epgstation/mirakurun/opt/bin

nano startup
chmod +x startup
~~~
`startup` の内容
~~~txt
#!/bin/bash

if !(type "recpt1" > /dev/null 2>&1); then
  apt update
  apt install -y --no-install-recommends git autoconf automake

  cd /tmp
  git clone https://github.com/stz2012/recpt1.git
  cd recpt1/recpt1
  ./autogen.sh
  ./configure --enable-b25 --prefix /opt
  make
  make install
fi

recpt1 -v
~~~

---
## 未実施項目

docker-mirakurun-epgstation の取得後に、`docker-mirakurun-epgstaion/mirakurun/opt/bin` へコピー
- recpt1
- recpt1ctl
- checksignal

https://github.com/stz2012/recpt1

### 録画コマンド (recpt1) の構築

~~~sh
git clone https://github.com/stz2012/recpt1.git

cd recpt1/recpt1
./autogen.sh
./configure --enable-b25
make
sudo make install

# 確認
which rectpt1
# > /usr/local/bin/rectpt1
~~~

動作確認
> 視聴できない (カードリーダーとB25のツールを未設定の為) ので、省略可

~~~sh
recpt1 --b25 --strip --device /dev/px4video0 --lnb 15 BS15_0 10 rec_bs15.ts
~~~
