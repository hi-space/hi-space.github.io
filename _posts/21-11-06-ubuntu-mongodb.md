---
title: "[Ubuntu] MongoDB Setup"
category: ENV
tags: env ubuntu
---

## Install

```sh
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list

sudo apt update
sudo apt install -y mongodb-org
```

## Execute

```sh
sudo systemctl start mongod
sudo systemctl status mongod

# autostart 
sudo systemctl enable mongod
```

## Check

```sh
mongo
```

<!--more-->
