---
title: JAVA ENV
tags: env
---

<!--more-->

# Env

- Ubuntu 20.04

# Install Java 

### 1. OpenJDK11 설치

```sh
sudo apt install openjdk-11-jdk
```

### 2. JAVA_HOME

```sh
# add symbolic link
which javac
readlink -f /usr/bin/javac

# vi gedit /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64

source /etc/profile
echo $JAVA_HOME
```

# Install Tomcat

```sh
sudo apt install tomcat9 tomcat9-admin
```

`localhost:8080` 에 접속해서 정상적으로 접속되는 것을 확인

### Service Commands

```sh
sudo systemctl disable tomcat9
sudo systemctl enable tomcat9
sudo systemctl restart tomcat9
```

### 방화벽

```sh
sudo ufw allow from any to any port 8080 proto tcp
```

### Admin Role

```sh
# suo vi /etc/tomcat9/tomcat-users.xml
# add this lines
<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<user username="tomcat" password="tomcat" roles="admin-gui,manager-gui"/>

# restart service
sudo systemctl restart tomcat9
```

- manager-gui: `http://localhost:8080/manager/html` 
- admin-gui: `http://localhost:8080/host-manager/html`
