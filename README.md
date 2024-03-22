# Setup Ping Pong Pi

This repo includes ansible playbooks to setup rpi4 for measuring ping/pong.

- `host` is executing ansible-playbook machine.
- `target` is rpi4.

## Prerequisite

1. Install Raspberry Pi Imager

https://www.raspberrypi.com/software/

2. Install ansible to host.

```sh
apt install ansible sshpass
```

## How to setup ping and pong

### prepare Ubuntu SDs

0. start Raspberry Pi Imager

1. select rpi4

2. select `Ubuntu Server 22.04 LTS(64-bit)`

<img src="assets/images/Screenshot from 2024-03-22 14-25-12.png">

3. select Storage, be carefull

<img src="assets/images/Screenshot from 2024-03-22 14-26-45.png">

4. click "次へ" and OS Customization

<img src="assets/images/Screenshot from 2024-03-22 14-27-50.png">

5. ユーザー名とパスワード設定（ユーザー名は ping/pong, パスワードは ubuntu とすると楽）

<img src="assets/images/Screenshot from 2024-03-22 14-28-04.png">

6. パスワード認証を使う

<img src="assets/images/Screenshot from 2024-03-22 14-28-19.png">

7. オプションは変更なし

<img src="assets/images/Screenshot from 2024-03-22 14-28-39.png">

8. 「保存」をクリックし、 「はい」をクリック

SD への書き込みが終了するのを待つ

### edit SDs

### Disable auto-upgrades

Open SD's `/etc/apt/apt.conf.d/20auto-upgrades` and make changes like following,

Before

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

After

```
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Unattended-Upgrade "0";
```

### Add netplan config

Make `/etc/netplan/99-custom-network.yaml` and write following,

```
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      optional: true
      dhcp4: false
      dhcp6: false
      addresses:
        - 192.168.0.101/24
      routes:
        - to: default
          via: 192.168.0.1
      nameservers:
        addresses:
          - 192.168.0.1
    wlan0:
      optional: true
```

- Installs needs WAN access, so we need to give the WAN accesible static IP, router's and name server's IP.
- ex. ping's IP is '192.168.0.101', pong's IP is '192.168.0.102'

### setup targets

1. make rpi4 accessible with SSH

```sh

 ┌────┐
 │PING├────────┐
 └────┘        │
               │
             ┌─┴─┐         ┌────┐
             │HUB├─────────┤HOST│
             └─┬─┘         └────┘
               │
 ┌────┐        │
 │PONG├────────┘
 └────┘

```

You need to access ping/pong with SSH by hand first time to resolve fingerprints.

2. run ansible playbooks, main.yml, on host.

```sh
ansible-playbook -i inventory.yml main.yml
```

## about ansible files

- ansible.cfg is ansible's configuration file, which is INI file.
- inventory.yml is target machine settings.
- main.yml is recipes for target.
