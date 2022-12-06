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
> OS
>>CentOS Linux release 7.2.1511 (Core)  
>> Linux 3.10.0-327.22.2.el7.x86_64  
>
>>Fedora release 25 (Twenty Five)  
>> Linux 4.9.4-201.fc25.x86_64  
>
>>Fedora release 27 (Twenty Seven)  
>> Linux 4.18.12-100.fc27.x86_64  
>
>>Fedora release 30 (Thirty)  
>> Linux 5.2.18-200.fc30.x86_64  

>Docker
>>version 1.11.2, build b9f10c9  
>>version 17.03.0-ce, build 60ccb22  
>>version 1.13.1, build 9c9378f-unsupported  
>>version 1.13.1, build 47e2230/1.13.1  

>Tuner
>>ISDB-S, ISDB-T Tuner PT3  

>Smart card reader
>>USB SmartCard Reader NTT Communications Corp. SCR3310-NTTCom  

## 利用方法
- 最新のdocker & docker-compose がインストール済
- SELinuxの無効化
- ホストマシンにPT3 Driverがインストール済
```
$ ls -l /dev/pt*video*
crw-rw-rw- 1 root video 246, 0 Jun 26 16:07 /dev/pt3video0
crw-rw-rw- 1 root video 246, 1 Jun 26 16:07 /dev/pt3video1
crw-rw-rw- 1 root video 246, 2 Jun 26 16:07 /dev/pt3video2
crw-rw-rw- 1 root video 246, 3 Jun 26 16:07 /dev/pt3video3
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
git clone https://github.com/Chinachu/docker-mirakurun-chinachu.git tvs
cd tvs
```
### 起動
```shell
docker-compose up -d
```
### 停止
```shell
docker-compose down
```

## startup script
```shell
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
