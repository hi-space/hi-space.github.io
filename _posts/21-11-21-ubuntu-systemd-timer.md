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

# References

- [systemd service timer 예제](https://twpower.github.io/213-systemd-timer-example)
- [systemctl 명령어](https://www.lesstif.com/system-admin/systemd-system-daemon-systemctl-24445064.html)
