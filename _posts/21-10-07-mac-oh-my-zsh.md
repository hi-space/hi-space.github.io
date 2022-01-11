---
title: "[Mac] Oh-my-zsh"
tags: mac env
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-07-mac-oh-my-zsh-theme.png
---

<!--more-->

## Install

```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## Setup

![](/assets/images/21-10-07-mac-oh-my-zsh-theme.png)

### 1. Appearance

#### 1-1. Theme

```sh
# vi ~/.zshrc
ZSH_THEME="agnoster"    # default: robbyrussell
```

#### 1-2. Font

기본 font면 아이콘이 깨지니까 다른 폰트 설치 필요

- [D2-coding-font](https://github.com/naver/d2codingfont)

1. `Preferences` - `Profiles` - `Text` 에서 Font 변경
2. `Use built-in Powerline glyphs` 체크

#### 1-3. Color Presets

```sh
# snazzy color theme
curl -LO https://raw.githubusercontent.com/mbadolato/iTerm2-Color-Schemes/master/schemes/Snazzy.itermcolors
```

1. [iTem2-Color-Schemes](https://github.com/mbadolato/iTerm2-Color-Schemes) 에서 원하는 Color preset 다운로드

2. `Preferences` - `Profiles` - `Colors` 에서 Color Presets import 후 해당 preset 선택

### 2. Productivity

#### 2-1. Remove User name

```sh
prompt_context() {
    if [[ "$USER" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
        prompt_segment black default "%(!.% { % F{yellow} % }.)$USER"
    fi
}
```

#### 2-2. Syntax highlighting

```sh
# install
brew install zsh-syntax-highlighting

# vi ~/.zshrc
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh 
source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh # case of M1
```

#### 2-3. Autosuggestion

```sh
brew reinstall zsh-autosuggestions
source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh # case of M1
```

#### 2-4. Status bar

- `Preferences` - `Profiles` - `Session` - `Status bar enabled` 체크
- `Preferences` - `Appearance` - `Status bar location` 위치 선택

# References

- [https://ooeunz.tistory.com/21](https://ooeunz.tistory.com/21)
