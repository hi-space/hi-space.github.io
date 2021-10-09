---
title: 마크다운 이미지 삽입 (Paste Image)
category: ENV
tags: env vscode
---

vscode에서 마크다운 편집할 때 이미지를 추가하기가 불편한다. 웹의 이미지나 데스크톱의 파일의 복사하여 바로 에디터에 붙여넣기 위한 extension 을 사용하자

<!--more-->

# Install

## 1. Ubuntu - xclip 설치

```sh
sudo apt install xclip
```

## 2. VScode - Paste Image 설치 

# How to Use

> ### Ctrl + Alt + V 

- 에디터에서 붙여넣기 하면 clipboard에 복사되어 있는 이미지가 자동으로 지정된 경로에 생성되고 해당 경로 이미지가 페이지에 삽입된다.
- 작성되어 있는 포스트 글씨를 블락한 채로 사용하면 그에 맞는 이름으로 이미지가 삽입된다.

# Settings

![](/assets/images/2021-09-04-vscode-paste-image-settings.png)

# Reference

- [https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image)
  