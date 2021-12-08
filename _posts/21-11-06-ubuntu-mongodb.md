---
title: "[Ubuntu] MongoDB Setup"
category: ENV
tags: env ubuntu db
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

## Troubleshooting

### mongodb (code=exited, status=14)

![](/assets/images/21-11-06-ubuntu-mongodb-2021-12-03-16-12-59.png)

```sh
sudo chown -R mongodb:mongodb /var/lib/mongodb
sudo chown mongodb:mongodb /tmp/mongodb-27017.sock
sudo service mongod restart
```

- [link](https://stackoverflow.com/questions/64608581/mongodb-code-exited-status-14-failed-but-not-any-clear-errors)
