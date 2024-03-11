# Ansible - WSL

`WSL 環境から実行`
  - Raspberry Pi に SSH 接続できること
  - Ansible を実行可能であること

1. [Ansible playbook をダウンロード](#ansible-playbook-をダウンロード)
1. [Ansible の実行環境を構築](#ansible-の実行環境を構築)
1. [Raspberry Pi をインベントリに登録](#raspberry-pi-をインベントリに登録)
1. [確認 - Ansible](#確認---ansible)
1. [録画環境構築 - Ansible playbook](#録画環境構築---ansible-playbook)

## Ansible playbook をダウンロード

~~~sh
git clone --depth1 https://github.com/tomosatoP/raspberrpi-tvrecorder.git
~~~

## Ansible の実行環境を構築

仮想環境 (venv) を使って Ansible 実行環境を構築

~~~sh
# 関連パッケージのインストール
sudo apt install python3-venv sshpass

cd raspberrpi-tvrecorder

# 仮想環境 (venv) - 作成
python3 -m venv venv --upgrade-deps

# 仮想環境 (venv) - 開始
. venv/bin/activate

# インストール
pip install ansible paramiko

# ansible.cfg 作成と編集
#   - inventory=inventory.yaml
#   - ssh_args=-C -o ControlMaster=auto -o ControlPersist=60s -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null
#   - callbacks_enabled=profile_tasks
ansible-config init --disabled -t all > ansible.cfg
sed -i.disabled -f ansible.cfg.script ansible.cfg

# 仮想環境 (venv) - 終了
deactivate
~~~

## Raspberry Pi をインベントリに登録

~~~sh
cd raspberrpi-tvrecorder

nano inventory.yaml
~~~
~~~yaml
recorder:
  hosts:
    <hostname>:
      ansible_host: <ip address>
      ansible_user: <username>
~~~

## 確認 - Ansible

~~~sh
cd raspberrpi-tvrecorder
. venv/bin/activate

# 確認
ansible-inventory --list

# 疎通確認
ansible recorder -m ping -k
# > SSH password: ****

# 環境変数など確認
ansible-playbook check.yaml -k
# > SSH password: ****
ansible-playbook check.yaml -b -k
# > SSH password: ****

deactivate
~~~

## 録画環境構築 - Ansible playbook

|実行順|roles/tag|内容|補足|
|---|---|---|---|
|1|raspi|日本語化<br>省電力化<br>cgproup v2制御有効化<br>スワップファイルのサイズ拡張|ja_JP.UTF8<br>OFF: WiFi, Bluetooth<br>CPU, Memory<br>8GByte|
|2|update|パッケージ更新||
|3|nas|NAS の自動接続設定|(BUFFALO LS510D65d)|
|4|px-q3u4|px-q3u4 用の非公式ドライバをインストール||
|5|docker-engine|docker-engine をインストール||
|6|app|docker-mirakurun-epgstation をダウンロード<br>compose.yaml を編集<br>mirakurun/conf/server.yml を作成<br>mirakurun/conf/tuners.yml を作成<br>mirakurun/conf/channels.yml を作成<br>startup スクリプトを導入<br>epgstaion/config/config.yml を編集|<br>px-q3u4, <br>WebAPI 有効化<br>px-q3u4<br>(笠岡放送)<br>recpt1等を build<br>WebAPI 有効化|
|7|start|docker-mirakurun-epgstation を開始|20 分ぐらいかかる、待つのだ|
~~~sh
cd raspberrpi-tvrecorder
. venv/bin/activate

ansible-playbook site.yaml -k
# > SSH password: ****

# 終了まで、だいたい 30 分ぐらい

deactivate
~~~

---