---
title: Go Project Structure
tags: go
---

공식적이지는 않으나 프로젝트 레이아웃 패턴에 자주 사용되는 패턴들이다. `/src` 폴더는 java에서 사용되는 패턴으로, 사용하지 않는 것이 좋다. `$GOPATH`에 `/pkg`, `/bin`, `/src` 디렉터리를 포함하고 있어서, 실제 프로젝트는 결국 `/src`의 서브디렉토리가 된다.   
프로젝트의 최상위 경로에 기본적으로 `go.mod`, `/cmd`, `/pkg`, `/internal` 가 위치해 있다고 보면 된다.

<!--more-->

## `/cmd`

- 메인 애플리케이션
- 디렉터리명은 애플리케이션의 실팽 파일 이름과 동일해야 한다.

## `/pkg`

- 외부 애플리케이션에서 사용되어도 되는 라이브러리 코드

## `/internal`

- 재사용을 원치 않는 내부 코드

## `/vendor`

- 애플리케이션 dependencies

## `/api`

- 프로토콜 정의 파일들 (OpenAPI/Swagger, JSON schema, etc.)

## `/web`

- 웹 어플리케이션의 컴포넌트
- static 한 web assets, server-side template, SPA, etc.

## `/configs`

- 설정 파일 템플릿, 기본 설정

## `/init`

- 시스템 init, 프로세스 매니저 등
- systemd, sysv, runit, supervisord, etc.

## `/scripts`

- 빌드, 설치, 분석, 기타 작업을 위한 스크립트

## `/build`

- Cloud, Docker, deb 등 패키징 파일이나 CI 스크립트
- `/build/ci`: CI 설정과 스크립트 (travis, circle, drone, etc.)

## `/deployments`

- IaaS, PaaS, 시스템과 컨테이너 오케스트레이션 배포 설정과 템플릿
- docker-compose, kubernetes, mesos, etc.

## `/test`

- 외부 테스트 앱들과 테스트 데이터들

## `/docs`

- 디자인과 사용자 문서들

## `/tools`

- 이 프로젝트에서 사용하는 도구들
- `/pkg`, `/internal` 에서 코드를 import 할 수 있음

## `/examples`

- 애플리케이션 혹은 공개된 라이브러리 예시들

## `/third_party`

- 사용하는 외부 도구들, 3rd party 유틸리티들 (ex, Swagger UI)

## `/assets`

- 이미지, 로고 등

# References

- [Standard Go Project Layout](https://github.com/golang-standards/project-layout)
 