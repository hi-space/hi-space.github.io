---
title: "Pulse Secure Clinet on Ubuntu 20.04"
tags: env
---

# Install

```sh
sudo dpkg --install pulse-9.1R5.x86_64.deb
sudo /usr/local/pulse/PulseClient_x86_64.sh install_dependency_packages

# dependency pacakges
sudo apt install libproxy1-plugin-webkit
sudo apt install libwebkitgtk-1.0
```

<!--more-->

# Start

```sh
/usr/local/pulse/pulseUi &
```

# Troubleshooting

#### rm: cannot remove '/usr/local/pulse/libwebp.so.6': No such file or director

dpkg 할 때 위와 같은 에러 발생

```sh
cd /usr/local/pulse/ 
sudo mkdir debs extra

cd debs
sudo wget http://archive.ubuntu.com/ubuntu/pool/main/i/icu/libicu60_60.2-3ubuntu3_amd64.deb 
sudo wget http://archive.ubuntu.com/ubuntu/pool/universe/w/webkitgtk/libjavascriptcoregtk-1.0-0_2.4.11-3ubuntu3_amd64.deb
sudo wget http://archive.ubuntu.com/ubuntu/pool/universe/w/webkitgtk/libwebkitgtk-1.0-0_2.4.11-3ubuntu3_amd64.deb

sudo dpkg -x debs/libicu60_60.2-3ubuntu3_amd64.deb ./extra
sudo dpkg -x debs/libjavascriptcoregtk-1.0-0_2.4.11-3ubuntu3_amd64.deb ./extra
sudo dpkg -x debs/libwebkitgtk-1.0-0_2.4.11-3ubuntu3_amd64.deb ./extra

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/pulse/extra/usr/lib/x86_64-linux-gnu/ 
sudo apt install libenchant1c2a

/usr/bin/env LD_LIBRARY_PATH=/usr/local/pulse:/usr/local/pulse/extra/usr/lib/x86_64-linux-gnu/:$LD_LIBRARY_PATH /usr/local/pulse/pulseUi
```

#### Couldn't find any package by glob 'libwebkitgtk-1.0'

1. `sudo nano /etc/apt/sources.list` 에 `deb http://cz.archive.ubuntu.com/ubuntu bionic main universe` 추가
2. `sudo apt update`
3. `sudo apt-get install libwebkitgtk-1.0-0`

# References

- [Quah-D-2020.06-Pulse-Secure-Client-on-Ubuntu-Linux](https://gist.github.com/DannyQuah/44df50362677ce7eb2c6fe1546dbef72)
