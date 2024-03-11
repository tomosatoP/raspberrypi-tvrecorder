# Raspberry Pi の環境設定

1. [NAS 自動接続設定](#nas-自動接続設定)
    1. [Samba-導入](#samba-導入)
    1. [マウント元作成](#マウント元作成)
    1. [マウント可否の確認](#マウント可否の確認)
    1. [自動マウント化](#自動マウント化)
1. [未実施項目](#未実施項目)
    1. [HWエンコードの有効化](#hwエンコードの有効化)

## NAS 自動接続設定

* NAS [LinkStation LS510D65d (192.168.68.168)]

### Samba 導入

~~~sh
sudo apt install -y samba cifs-utils
~~~

### マウント元作成

~~~sh
sudo mkdir /mnt/nas
sudo chmod 777 /mnt/nas
~~~

### マウント可否の確認
~~~sh
sudo mount -t cifs //192.168.68.168/rpitvrec /mnt/nas -o iocharset=utf8,vers=2.0
# パスワードを聞いてくるが、そのままリターン

# マウントの確認
df -h

# マウント解除
sudo umount /mnt/nas
~~~

### 自動マウント化

> uid, gidは、id コマンドで確認
>  * $ id => uid=1000(pi) gid=1000(pi) ...
>  * $ sudo id => uid=0(root) gid=0(root) ...

~~~sh
sudo nano /etc/fstab
~~~
~~~diff
+ //192.168.1.6/rpitvrec /mnt/nas cifs iocharset=utf8,vers=2.0,username="",password=,file_mode=0777,dir_mode=0777,uid=1000,gid=1000,defaults 0 0
~~~

---
## 未実施項目

### HWエンコードの有効化

~~~sh
sudo nano /etc/udev/rules.d/10-vchiq-permissions.rules
~~~
~~~diff
+ SUBSYSTEM=="vchiq",GROUP=="video",MODE=="0666"
~~~