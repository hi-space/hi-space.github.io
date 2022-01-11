---
title: Mac Jekyll 환경 설정
category: ENV
tags: env mac m1
---

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
bundler install
bundle exec jekyll serve
```

<!--more-->

# Reference

- [M1 MAC에서 Jekyll 블로깅을 위한 로컬 환경 구축하기](https://unluckyjung.github.io/develop-setting/2021/01/20/Mac-Jekyll-Setting/)
