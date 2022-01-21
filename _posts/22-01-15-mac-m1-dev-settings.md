---
title: Mac M1 개발 환경세팅
tags: env mac
---

# VS code

```sh
brew install visual-studio-code --cask
```

# Node.js

```sh
brew install node
npm install -g npm
npm -version
npm install -g yarn
```

# Python

```sh
wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-MacOSX-arm64.sh
bash Miniforge3-MacOSX-arm64.sh 
```

# Jekyll

```sh
# install
xcode-select --install
brew install gcc rbenv ruby-build
rbenv install 2.6.9 # rbenv install -l
rbenv global 2.6.9

# vi ~/.zshrc
export PATH={$Home}/.rbenv/bin:$PATH && eval "$(rbenv init -)"

# build
gem install bundler
rbenv rehash
```

# Java

```sh
brew tap AdoptOpenJDK/openjdk
brew install --cask adoptopenjdk8
/usr/libexec/java_home -v 1.8
java -version
```
