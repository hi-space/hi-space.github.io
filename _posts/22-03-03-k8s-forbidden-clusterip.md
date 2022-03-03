---
title: "The “spec.ports[0].nodePort: Forbidden: may not be used when type is ‘ClusterIP'”"
tags: k8s troubleshooting
---

### Problem 

```sh
The Service "test-svc" is invalid: spec.ports[0].nodePort: Forbidden: may not be used when `type` is 'ClusterIP'
```

Service 타입을 `NodePort`로 적용했었다가 `ClusterIP`로 변경하고 apply 했을 때 문제가 발생했다. 

### Solution

```sh
k apply -f test-svc.yaml --force
```

`--force`를 붙여주면 이전의 service 는 삭제되고 새롭게 등록되어 문제가 해결된다.

<!--more-->
