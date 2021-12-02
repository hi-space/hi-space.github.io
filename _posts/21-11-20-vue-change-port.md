---
title: "[Vue] 접속 port 변경"
tags: vue frontend
---

vue를 실행시키면 기본적으로 8080 port로 실행이 된다. 실행 port를 변경해주기 위해 npm 또는 package.json 파일을 수정해준다. 

## 1. npm 

```sh
npm run serve --port 8000
```

## 2. `package.json`

```sh
"scripts": {
    "serve": "vue-cli-service serve --port 8000",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
}
```

<!--more-->

여기서 port를 80으로 바꿔주게 되면 80 port가 아닌 1024 포트로 실행이 된다.

port 1~1023 까지는 Well-Known ports (또는 시스템 포트)로, 국제 도메인 관리리구에 의해 통제된다. 사용하기 위해서는 sudo 권한이 필요하다.

> 1) Well-Known ports (0 ~ 1023)   
> 시스템 포트라고도 불리며, ICANN(nternet Corporation for Assigned Names and Numbers: 국제 도메인 관리기구)에 의해 통제된다.
> 대중적인 네트워크 서비스를 제공하는 시스템 프로세스에서 사용되는 포트번호들이며, Unix계열 운영체제에서 사용하기 위해선 반드시 수퍼유저(SUDO)권한으로 실행되어야 한다.

> 2) Registered ports (1024 ~ 49151)   
> ICANN 산하기관인 IANA에 등록된 포트번호들이다. 등록은 되어있지만 Well-known ports들 처럼 직접 통제당하진 않은 포트번호들이다.   
> ex) 3306 (mysql), 1521 (oracle), 8000(django), 5000(flask)

> 3) Private ports (49152 ~ 65535)   
> IANA에 등록되지 않은 동적포트들

그래서 80 포트로 실행시키기 위해서는 아래와 같이 실행시키면 된다.

```sh
sudo npm run server -- --port 80
```
