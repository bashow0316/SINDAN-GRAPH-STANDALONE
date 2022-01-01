# SINDAN-GRAPH-STANDALONE(ver.0.1.1)

MEMO

## Archtecture

Internet monitoring tool by use Raspberry Pi 4 for Raspbian OS.

Fig(T.B.D.)

Component

- [SINDAN-CLIENT](https://github.com/SINDAN/sindan-client)
- [Elasticsearch OSS 7.10.2](https://www.elastic.co/downloads/past-releases/elasticsearch-oss-7-10-2)
- [Kibana OSS 7.10.2](https://www.elastic.co/downloads/past-releases/kibana-oss-7-10-2)
- [Logstash OSS 7.16.2](https://www.elastic.co/downloads/past-releases/logstash-oss-7-16-2)

T.B.D.

- [Docker](https://www.docker.com/)
  - [Elasticsearch-oss](https://www.docker.elastic.co/r/elasticsearch/elasticsearch-oss)
  - [Logstach-oss](https://www.docker.elastic.co/r/logstash/logstash-oss)
  - [Kibana-oss](https://www.docker.elastic.co/r/kibana/kibana-oss)

## Quickstart

### Operate on MAC/Linux

- Download and Setup Raspbian OS(raspios)

[Raspberry Pi OS Lite](https://www.raspberrypi.com/software/operating-systems/)

- Unzip raspbian OS

``` sh
unzip 'YYYY-mm-dd-raspios-bullseye-armhf-lite.zip'
```

- Check SD card mountpoint and unmounting

``` sh
diskutil list
```

- Setup the image to SD card

``` sh
sudo dd if='YYYY-mm-dd-raspios-bullseye-armhf-lite.img' of='SD cord' bs=1m
```

#### Raspbian Reference

https://www.raspberrypi.com/documentation/computers/getting-started.html

### Operate on Raspberry Pi 4 for Raspbian OS

- Use wlan0(WiFi)

``` sh
sudo rfkill list
sudo rfkill unblock wlan0
```

``` sh
wpa_passphrase "SSID" "PASSWORD" | sudo tee -a /etc/wpa_supplicant/wpa_supplicant.conf
```

``` sh
reboot
```

- setup timezone

``` sh
sudo timedatectl set-timezone Asia/Tokyo
```

- apt install

``` sh
sudo apt install -y git tmux
```

- Setup SINDAN-CLIENT

``` sh
cd
git clone https://github.com/SINDAN/sindan-client.git
cd sindan-client/linux/
cp sindan.conf.example sindan.conf
./install.sh
cd
```

- Measurement of once every 5 minutes.
use crontab

``` sh
echo 'SHELL=/bin/sh' | sudo tee -a /etc/cron.d/sindan
echo 'PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin' | sudo tee -a /etc/cron.d/sindan
echo '*/5 * * * * root /home/pi/sindan-client/linux/sindan.sh  1>/dev/null 2>/dev/null' | sudo tee -a /etc/cron.d/sindan
```

- Setup ELK

``` sh
sudo apt install openjdk-11-jdk
echo 'export JAVA_HOME="/usr/lib/jvm/java-11-openjdk-armhf"' >> ~./bashrc
echo 'export PATH="$JDK_HOME/bin$PATH"' >> ~./bashrc
source source ~/.bashrc
```

``` sh
cd
mkdir -p elastic
cd elastic/
```

``` sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-oss-7.10.2-linux-aarch64.tar.gz
wget https://artifacts.elastic.co/downloads/kibana/kibana-oss-7.10.2-linux-aarch64.tar.gz
wget https://artifacts.elastic.co/downloads/logstash/logstash-oss-7.16.2-linux-aarch64.tar.gz
```

``` sh
tar xvzf elasticsearch-oss-7.10.2-linux-aarch64.tar.gz
tar xvzf kibana-oss-7.10.2-linux-aarch64.tar.gz
tar xvzf logstash-oss-7.16.2-linux-aarch64.tar.gz
```

Check ELK

Use Tmux

### Window:1

- Setup Elasticsearch

``` sh
cd ~/elastic/elasticsearch-7.10.2
./bin/elasticsearch
```

### Window:2

- Setup Kibana

``` sh
cd ~/elastic/kibana-7.10.2-linux-aarch64
cd node/bin/
sudo npm install -g n
sudo n install 10.23.1
mv node node.orgin
ln -s `which node` node
cd ~/elastic/kibana-7.10.2-linux-aarch64
sed -i 's/#server.host: "localhost"/server.host: "0.0.0.0"/g' config/kibana.yml
./bin/kibana
```

- iptables

``` sh
sudo apt install -y iptables
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5601 -j ACCEPT
```

#### Elastic Reference

https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html#targz

### Memo

- Update node

``` sh
curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -
sudo apt update -y && sudo apt install -y yarn
sudo apt --fix-broken install -y
sudo apt autoremove -y
sudo apt install -y libnode72
sudo apt install -y nodejs npm
```

- Install Docker

``` sh
curl -sSL https://get.docker.com | sh
docker -v
```

- iptables-persistent

``` sh
sudo apt install -y iptables-persistent
```

- ufw

``` sh
sudo apt install ufw
```
