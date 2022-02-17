---
title: "k8s Service Unavailable: HTTP status code 503"
tags: k8s troubleshooting
---

```sh
Failed to list services:
rpc error: code = Unavailable desc = Service Unavailable: HTTP status code 503;
transport: received the unexpected content-type "text/html"
```

k8s에 gRPC 서버를 서빙하기 위해 tls 설정해서 배포하도록 했는데 클라이언트에서 접속 시도했을 때 위와 같이 에러가 발생했다.
이전에도 한번 발생했었는데 그 때는 app이름을 잘못 기입해서 발생한 문제였다. 결과적으로 보면 이번에도 유사한 문제였다...

<!--more-->

`k get endpoints` 를 했을 때 svc의 ENDPOINTS가 None으로 나오는 것을 확인했다. ENDPOINTS는 별다른 설정을 하지 않았다면 자동으로 할당이 되어야 하는데, deploy 설정에 뭔가 문제가 있을 때는 None으로 나온다.

nginx가 잘못 가리키는 줄 알고 ingress 설정을 많이 의심했었는데 문제는 deployment였다. selector의 label name 이 잘못되어 있었다.

- 문제의 deployment, service

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test-server
  template:
    metadata:
      labels:
        app: test-server

---

kind: Service
metadata:
  labels:
    app: test-server
  name: test-server-svc
```

- 수정한 deployment, service (`app`을 `name`으로 변경)

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      name: test-server
  template:
    metadata:
      labels:
        name: test-server

---

kind: Service
metadata:
  labels:
    name: test-server
  name: test-server-svc
```

수정한 이후에 재배포 했을 때는 `Unidentified error: The Deployment is invalid` 에러가 발생했는데, 클러스터에 동일한 deployment, service가 올라가 있는 상황에서 `spec.selector.matchLabels.app`, `spec.template.metadata.Labels.app`, `spec.template.spec.containers.name` 만 변경해서 apply로 적용할 경우 이와 같은 문제가 발생한다. 

클러스터에 올라가 있는 deployment, service를 삭제한 후 재배포하면 정상적으로 배포가 완료된다.
- `k delete deployemnt {deployment_name}`
- `k delete svc {service_name}`

전체 k8s 설정은 다음과 같다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: test-server
  name: test-server
spec:
  replicas: 1
  selector:
    matchLabels:
      name: test-server
  template:
    metadata:
      labels:
        name: test-server
    spec:
      containers:
      - name: test-server
        image: test-server:latest
        imagePullPolicy: Always
        ports:
        - containerPort: 9090
          protocol: TCP

---

apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: test-server-ingress
  annotations:
    kubernetes.io/ingress.class: "default-ingress"
    nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
spec:
  rules:
    - host: test-server.hispace.kr
      http:
        paths:
          - path: /
            backend:
              serviceName: test-server-svc
              servicePort: 9090
  tls:
  - secretName: hispace-tls
    hosts:
      - test-server.hispace.kr

---

apiVersion: v1
kind: Service
metadata:
  labels:
    name: test-server
  name: test-server-svc
spec:
  type: NodePort
  ports:
  - name: http
    port: 80
    targetPort: 8000
  - name: grpc
    port: 9090
    targetPort: 9090
  selector:
    name: test-server
```
