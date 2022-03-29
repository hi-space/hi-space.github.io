---
title: "MAC 에서 VS Code를 Unity 에디터로 사용하기"
tags: env mac unity 
---

<!--more-->

## 1.  VS Code 설정

- 플러그인에서 Unity, C# 관련 설치
- C# 설치 할 때 .NET이 설치되지 않았으면 따로 [설치 페이지](https://dotnet.microsoft.com/en-us/download)에서 설치해준다.
- 터미널 창에서 mono와 dotnet이 정상적으로 설치되었는지 확인해준다.

    ```csharp
    mono --version
    dotnet --version
    ```

- VS Code 의 `Preferences`- `Settings` 에서 Use Global Mono 설정을 Always로 변경해준다.

    > **Omnisharp: Use Global Mono**
    Launch OmniSharp with the globally-installed Mono.  
	If set to "always", "mono" version 6.4.0 or greater must be available on the PATH. If set to "auto", OmniSharp will be launched with "mono" if version 6.4.0 or greater is available on the PATH.

## 2. Unity 설정

- `Preferences` - `External Tools` 에서 External Script Editor로 Visul Studio Code로 설정
- `Assets` - `Open C# Project` 로 VS Code가 열리고 자동완성이 되는지 확인
