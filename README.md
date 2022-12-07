# docker-mirakurun-chinachu
Mirakurun と Chinachu をDockerコンテナに閉じ込めました

## Constitution
### Mirakurun
- [Mirakurun](https://github.com/Chinachu/Mirakurun)
  - https://github.com/Chinachu/Mirakurun/blob/master/docker/Dockerfile

### Chinachu
- Alpine Linux 3.8(node:14-alpine)
- [Chinachu](https://github.com/Chinachu/Chinachu)
  - branch: gamma

## 動作確認環境

```shell
# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=22.04
DISTRIB_CODENAME=jammy
DISTRIB_DESCRIPTION="Ubuntu 22.04.1 LTS"

# docker -v
Docker version 20.10.21, build baeda1f
```

### チューナー
* PX-MLT5PE 

### Smart card reader
* SCM ICカードリーダー/ライター B-CAS・住基カード対応 SCR3310/v2.0 ([Amazon.co.jp])(https://www.amazon.co.jp/gp/product/B0085H4YZC)  

## 利用方法
- 最新のdockerがインストール済 (docker compose コマンドがない場合は docker-compose も必要)
- SELinuxの無効化
- px4_drv導入済み

```
# ls -l /dev/pxm*
crw-rw-r-- 1 root video 236, 0 11月  8 17:23 /dev/pxmlt5video0
crw-rw-r-- 1 root video 236, 1 11月  8 17:23 /dev/pxmlt5video1
crw-rw-r-- 1 root video 236, 2 11月  8 17:23 /dev/pxmlt5video2
crw-rw-r-- 1 root video 236, 3 11月  8 17:23 /dev/pxmlt5video3
crw-rw-r-- 1 root video 236, 4 11月  8 17:23 /dev/pxmlt5video4
```

- B-CAS 用に利用するスマートカードリーダーはMirakurunコンテナ内で管理しますので  
ホストマシン上のpcscdは停止してください
```
sudo systemctl stop pcscd.socket
sudo systemctl disable pcscd.socket
```

- docker-composeを利用しておりますので、プロジェクトディレクトリ内で下記コマンドを実行してください  
プロジェクトディレクトリ名はビルド時のレポジトリ名になりますので、適当に短いフォルダ名が推奨です

### 取得例
```shell
git clone https://github.com/NAKNAO-nnct/docker-mirakurun-chinachu.git tvs
cd tvs
```

#### startup script
```shell
sudo mv -vf /usr/local/mirakurun /opt/mirakurun
sudo mkdir -p /opt/mirakurun/run /opt/mirakurun/opt /opt/mirakurun/config /opt/mirakurun/data

mkdir -p /opt/mirakurun/opt/bin
cp mirakurun/startup /opt/mirakurun/opt/bin/startup
chmod +x /opt/mirakurun/opt/bin/startup

docker-compose down
docker-compose run --rm -e SETUP=true mirakurun
docker-compose up -d
```

## 設定
エリア、環境によって変更が必要なファイルは下記の通りとなります
### Mirakurun
- ポート番号 : 40772
- mirakurun/conf/tuners.yml  
チューナー設定
- mirakurun/conf/channels.yml  
チャンネル設定

### Chinachu
- ポート番号 : 10772, 20772(local network only), 5353/udp(mDNS)
- chinachu/conf/config.json  
チューナー設定  
チャンネル設定

### 録画ファイル保存先
また録画ファイルはプロジェクトフォルダ内の./recordedに保存されます  
> 保存先を別HDDにしたい場合は、docker-compose.ymlの
>> ./recorded:/usr/local/chinachu/recorded
>
> の./recordedを変更することで保存先を変更可能

## License
This software is released under the MIT License, see LICENSE.
