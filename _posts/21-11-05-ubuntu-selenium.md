---
title: "[Ubuntu] Selenium Setup"
category: ENV
tags: env ubuntu
---

<!--more-->

# Command lines

## Install Chrome

```sh
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
sudo sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
sudo apt-get update
sudo apt-get install google-chrome-stable
```

## Install chromedriver

```sh
google-chrome --version
wget -N http://chromedriver.storage.googleapis.com/76.0.3809.68/chromedriver_linux64.zip -P ~/Downloads
unzip ~/Downloads/chromedriver_linux64.zip
```

## Install pyvirtualdisplay

```sh
pip install xlrd
sudo apt install xvfb
pip install pyvirtualdisplay
pip install webdriver-manager
```

## Usages

```py
from selenium import webdriver
from pyvirtualdisplay import Display
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.maximize_window()
```

---

```sh
#xvfb, jdk 등 설치
sudo apt-get update
sudo apt-get install -y unzip xvfb libxi6 libgconf-2-4
sudo apt-get install default-jdk 

# Install chrome
sudo curl -sS -o - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add sudo echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list
sudo apt-get -y update
sudo apt-get -y install google-chrome-stable 

# Install chromedriver
google-chrome --version
# https://sites.google.com/a/chromium.org/chromedriver/downloads 
wget https://chromedriver.storage.googleapis.com/79.0.3945.36/chromedriver_linux64.zip unzip chromedriver_linux64.zip 

sudo mv chromedriver /usr/bin/chromedriver
sudo chown root:root /usr/bin/chromedriver
sudo chmod +x /usr/bin/chromedriver 

wget https://selenium-release.storage.googleapis.com/3.13/selenium-server-standalone-3.13.0.jar 

mv selenium-server-standalone-3.13.0.jar selenium-server-standalone.jar
xvfb-run java -Dwebdriver.chrome.driver=/usr/bin/chromedriver -jar selenium-server-standalone.jar 

chromedriver --url-base=/wd/hub
```

# Docker

```sh
docker pull selenium/standalone-chrome

docker run --name my_selenium -it -p 8888:8888 -v C:\LOCAL_MACHINE_SHARE_DIR:/home/seluser selenium/standalone-chrome /bin/bash

sudo apt-get update
sudo apt-get install vim
sudo apt-get install python-pip

pip install jupyter
pip install pandas

pip install requests
pip install xmltodict
pip install openpyxl
pip install pyvirtualdisplay
pip install selenium
pip install bs4
pip install requests

cd /home/seluser/
export PATH=$PATH:~/.local/bin
```
