---
title: "systemd 을 이용한 timer service"
tags: ubuntu 
---

systemd 서비스를 특정 시기나 주기적으로 실행시키기 위해 timer service를 세팅한다. 

<!--more-->

## 1. Service 생성

```sh
# /etc/systemd/system/periodic-task.service
[Unit]
Description=Crawling job that needs periodic execution

[Service]
Type=oneshot
ExecStart=/tmp/crawling.sh

[Install]
WantedBy=multi-user.target
```

- `Type`: 실행할 서비스의 타입
  - `simple`
  - `oneshot` : 한번 사용하고 끝낼 서비스. 서비스의 프로세스가 끝날때 까지 대기. 사용할 때 activating 되고 끝나면 inactive 된다.
  - `forking`
- `WantedBy`: Unit이 어떻게 활성화 될것인지 설정

### Unit

- Description : 서비스 설명
- After : 본 서비스 이전에 시작할 서비스
- Before : 본 서비스 이후에 시작할 서비스
- Requires : 본 서비스와 의존관계의 서비스로 반드시 실행되어야 본서비스가 실행
- Wants : Requires보다 약한 의존관계로 설령 서비스가 실행되지 않더라도 본서비스가 실행됨

### Service

- Type : Type 에는 simple, forking, oneshot, dbus, notify, idle 중에 1개를 입력
- simple : deafult값. 유닛이 시작된 경우 즉시 systemd는 유닛의 시작이 완료 되었다고 판단함. 다른 유닛과 통신하기 위해 소켓을 사용하는 경우 이 설정을 하면안됌
- forking : 자식 프로세스가 생성완료 되는 단계까지를 systemd가 시작완료 되었다고 판단하며 부모 프로세스를 추적할 수 있도록 PIDFile 필드에 PID파일을 선언해주어야함
- oneshot : 메인 프로세스가 시작되면 상태를 activating 으로 바꾸고 끝날때까지 기다리며 해당 메인 프로세스가 끝나야지만 다음 systemd unit으로 넘어간다. 또한 해당 실행이 종료되더라도 RemainAfterExit=yes 옵션을 통해 유닛이 활성화 상태로 간주할 수 있다.
- dbus : simple과 유사, DBUS에 지정된 BusName이 준비될 때까지 대기하며 DBUS 준비가 완료된 이후에 프로세스 시작
- notify : simple과 유사, systemd에 시그널을 보내고 시그널에 대한 내용은  libsystemd-daemon.so 에 선언 되어있음
- idle : simple과 유사, 모든 서비스가 실행된 후에 실행됨
- EnvironmentFile : 서비스의 환경설정을 파일을 지정
- ExecStartPre : 서비스 시작하기 전에 실행할 명령을 설정
- ExecStart : 서비스를 시작하기 위한 full path 및 실행 인자를 설정
- ExecStartPost : 서비스를 시작한 이후에 실행할 명령을 설정
- ExecStop : 서비스를 종료할 때 실행할 명령을 설정
- ExecStopPost : 서비스 종료 명령 이후에 실행할 명령을 설정
- ExecReload : 서비스가 reload 될때 필요한 명령어나 스크립트를 지정
- KillMode : 프로세스가 어떻게 중지 되는지 결정함
- Restart : on-failure는 어떤 문제로 인해 0이아닌 Exit코드를 보여주고 중지될 경우 서비스를 다시시작, 반대로 on-success는 프로세스가 아무런 문제없이 Exit코드가 0인 경우 다시 그 서비스를 시작하라는 의미
- RestartSec : 서비스를 다시 시작하기전에 이 시간동안 서비스를 Sleep상태로 두라는 의미
- RemainAfterExit : (yes|no) 유닛이 종료 이후에도 유닛이 활성화 상태로 판단함
- TimeoutSec : 서비스 종료시 대기하는 시간

### Install

부팅 시에 Unit이 활성화나 비활성화를 위해 사용하는 명령어. 값이 default.target이면 링크 파일을 생성하지 않고 multi-user.target 이면 링크 파일을 생성함

## 2. Timer 생성

timer의 이름은 service 의 이름과 동일해야 한다.

```sh
# /etc/systemd/system/periodic-task.timer
[Unit]
Description = Timer that periodically triggers crawling.service

[Timer]
OnCalendar=*-*-* 08:00:01
Persistent=true

[Install]
WantedBy=timers.target
```

- `OnCalendar`: 년-월-일 시:분:초

## 3. Systemd 등록

```sh
systemctl enable periodic-task.service
systemctl enable periodic-task.timer
systemctl daemon-reload
systemctl start periodic-task.timer
```

`/etc/systemd/system/` 에 위치하게 되면 시스템 전역 서비스로 root 권한으로 실행된다.   
유저 권한으로 사용하기 위해서는 `~/.config/systemd/user/` 에 서비스 파일들을 위치시키고 `--user`를 추가하여 커맨드를 사용한다.

```sh
systemctl --user restart periodic-task.timer
```

## 4. 관련 커맨드

```sh
systemctl status {service-name}.service # 상태
systemctl -l status {service-name}.service # 로그
journalctl -xefu {service-name}.service # tail log
```

# References

- [systemd service timer 예제](https://twpower.github.io/213-systemd-timer-example)
- [systemctl 명령어](https://www.lesstif.com/system-admin/systemd-system-daemon-systemctl-24445064.html)
- [systemd란? systemd unit파일 작성 방법](https://kim-dragon.tistory.com/202)