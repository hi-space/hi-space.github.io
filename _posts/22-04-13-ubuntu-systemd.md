---
title: "systemd unit"
tags: ubuntu 
---

systemd(system daemon)은 Unix 시스템이 부팅후에 가장 먼저 생성된 후에 다른 프로세스를 실행하는 init 역할을 대체하는 데몬이다.
부팅 시 필요한 작업을 systemd unit으로 등록하여 살 수 있다.

<!--more-->

## systemd unit 위치

```sh
# root의 경우
cd /etc/systemd/system/system-name.service

# user의 경우
cd ~/.config/systemd/user/system-name.service
```

## systemd의 sections

`Unit`, `Service`, `Install`, `Socket`, `Mount`, `Automount`, `Swap`, `Path`, `Timer`, `Slice`

### Unit

- Description : 서비스 설명
- After : 본 서비스 이전에 시작할 서비스
- Before : 본 서비스 이후에 시작할 서비스
- Requires : 본 서비스와 의존관계의 서비스로 반드시 실행되어야 본서비스가 실행
- Wants : Requires보다 약한 의존관계로 설령 서비스가 실행되지 않더라도 본서비스가 실행됨

### Service

- `Type` : Type 에는 simple, forking, oneshot, dbus, notify, idle 중에 1개를 입력
  - `simple` : deafult값. 유닛이 시작된 경우 즉시 systemd는 유닛의 시작이 완료 되었다고 판단함. 다른 유닛과 통신하기 위해 소켓을 사용하는 경우 이 설정을 하면안됌
  - `forking` : 자식 프로세스가 생성완료 되는 단계까지를 systemd가 시작완료 되었다고 판단하며 부모 프로세스를 추적할 수 있도록 PIDFile 필드에 PID파일을 선언해주어야함
  - `oneshot` : 메인 프로세스가 시작되면 상태를 activating 으로 바꾸고 끝날때까지 기다리며 해당 메인 프로세스가 끝나야지만 다음 systemd unit으로 넘어간다. 또한 해당 실행이 종료되더라도 RemainAfterExit=yes 옵션을 통해 유닛이 활성화 상태로 간주할 수 있다.
  - `dbus` : simple과 유사, DBUS에 지정된 BusName이 준비될 때까지 대기하며 DBUS 준비가 완료된 이후에 프로세스 시작
  - `notify` : simple과 유사, systemd에 시그널을 보내고 시그널에 대한 내용은  libsystemd-daemon.so 에 선언 되어있음
- `idle` : simple과 유사, 모든 서비스가 실행된 후에 실행됨
- `EnvironmentFile` : 서비스의 환경설정을 파일을 지정
- `ExecStartPre` : 서비스 시작하기 전에 실행할 명령을 설정
- `ExecStart` : 서비스를 시작하기 위한 full path 및 실행 인자를 설정
- `ExecStartPost` : 서비스를 시작한 이후에 실행할 명령을 설정
- `ExecStop` : 서비스를 종료할 때 실행할 명령을 설정
- `ExecStopPost` : 서비스 종료 명령 이후에 실행할 명령을 설정
- `ExecReload` : 서비스가 reload 될때 필요한 명령어나 스크립트를 지정
- `KillMode` : 프로세스가 어떻게 중지 되는지 결정함
- `Restart` : on-failure는 어떤 문제로 인해 0이아닌 Exit코드를 보여주고 중지될 경우 서비스를 다시시작, 반대로 on-success는 프로세스가 아무런 문제없이 Exit코드가 0인 경우 다시 그 서비스를 시작하라는 의미
- `RestartSec` : 서비스를 다시 시작하기전에 이 시간동안 서비스를 Sleep상태로 두라는 의미
- `RemainAfterExit` : (yes|no) 유닛이 종료 이후에도 유닛이 활성화 상태로 판단함
- `TimeoutSec` : 서비스 종료시 대기하는 시간

## Example service

```sh
# sudo vi /etc/systemd/system/system-name.service
[Unit]
Description=Test Service
After=multi-user.target

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/home/root/ws/
ExecStart=/home/root/ws/scripts/run.sh

[Install]
WantedBy=multi-user.target
```

`multi-user.target`은 systemd service가 runlevel 2에 도달했을 때를 의미한다. 즉, 부팅이 됐을 때 해당 서비스가 시작된다.

```sh
# vi ~/.config/systemd/user/system-name.service
[Unit]
Description=Test User Service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/home/ubuntu/ws/
ExecStart=/home/ubuntu/ws/start

[Install]
WantedBy=default.target
```

user systemd에는 `multi-user.target`이 없기 때문에 `default.target`으로 설정해줘야야, 부팅 시에 서비스가 시작된다.

## system service 등록

```sh
systemctl enable service-name.service
systemctl daemon-reload
systemctl start periodic-task.timer
```

user system service인 경우 `systemctl --user` 파라미터를 붙여주면된다.

## 등록된 service 확인

```sh
sudo systemctl list-units --state=enabled
sudo systemctl list-units --state=failed
sudo systemctl list-units --state=active
sudo systemctl list-units --state=inactive
sudo systemctl list-units --state=running
```

state 값에는 `enabled`, `failed`, `active`, `inactive`, `running`이 올 수 있다.

## system service 로그

```sh
systemctl -l status 
journalctl -xefu {service-name}.service # tail log
```

- systemctl의 `-l`: 로그 항목을 ...으로 자르지 않고 전체를 보여주겠다는 옵션이다.
- journalctl `-x`: 추가적인 메시지(에러 or 이벤트) 표시
- journalctl `-e`: 로그의 가장 끝 부분 확인
- journal `-u`: 특정 unit의 로그 확인
- journal `-f`: 로그를 계속 확인하며 트래킹
- journal `-n`: 최근 n 개의 메시지만 표시
- journal `--since`, `--until`: 특정 기간내의 메시지 확인
  - ex) `journalctl --since 2020-01-09 --until yesterday`
  - ex) `journalctl --since "-2hour" --until "10min"`
