---
title: "[Ubuntu] Theme - tweak"
category: ENV
tags: env ubuntu
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-01-ubuntu-tweak-theme-2021-10-01-16-09-48.png
---

Ubuntu를 더 예쁘고 효율적으로 사용하자

<!--more-->

## 1. Gnome tweak tool 설치

```sh
sudo apt install gnome-tweak-tool
```

## 2. 테마

- ### [WhiteSur GTK Theme](https://github.com/vinceliuice/WhiteSur-gtk-theme)

![](/assets/images/21-10-01-ubuntu-tweak-theme-2021-10-01-16-09-48.png)

Tweaks - Appearance에서 설치한 테마를 선택해주면 된다.

## 3. [Gnome Extensions](https://extensions.gnome.org/)

원하는 확장 플러그인을 다운받아 사용할 수 있다. 아래와 같은 플러그인들을 설치해서 사용 중. 테마도 깔끔한 장점이 있지만 screenshot 기능(Screenshot tool)이나 clipboard 기록을 볼 수 있는 플러그인(Clipboard indicator), 모니터 별로 workspace 를 사용할 수 있는 등 여러 커스톰 편집이 가능한 점이 좋다.

- [User Themes](https://extensions.gnome.org/extension/19/user-themes/)
- [Extensions](https://extensions.gnome.org/extension/1036/extensions/)
- [Dash to panel](https://extensions.gnome.org/extension/1160/dash-to-panel/)
- [Floating Dock](https://extensions.gnome.org/extension/3730/floating-dock/)
- [Clipboard indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/)
- [Screenshot tool](https://extensions.gnome.org/extension/1112/screenshot-tool/)

## 4. Isolate workspace

```sh
gsettings set org.gnome.shell.app-switcher current-workspace-only true
gsettings set org.gnome.shell.extensions.dash-to-dock isolate-workspaces true
```