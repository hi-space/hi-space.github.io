---
title: "[Ubuntu] 공유폴더를 위한 samba 설치"
category: ENV
tags: env ubuntu
---

공유폴더를 위한 samba 설치

<!--more-->

## 1. Install

```sh
sudo apt install samba
```

## 2. Register a user

```sh
sudo smbpasswd -a <아이디>
```

## 3. Edit a config file

```sh
# sudo vi /etc/samba/smb.conf

[share]
comment = share directory
path = /home/yoo/share
public = yes
writeable = yes
read only = no
create mode = 0777
directory mode = 0777
```

## 4. Restart samba service

```sh
sudo service smbd restart
```
