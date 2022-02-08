---
title: "k8s 세팅: go + grpc"
tags: go k8s
---

<!--more-->

## Deployment.yaml

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
        image: golang:1.17
        imagePullPolicy: Always
        ports:
        - containerPort: 9000
          protocol: TCP
```

```sh
kubectl apply -f deployment.yaml
```

## service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    name: test-server
  name: test-server-service
spec:
  type: NodePort
  ports:
  - name: grpc
    port: 9000
    targetPort: 9000
  selector:
    name: test-server
```

```sh
kubectl apply -f service.yaml
```

## ingress.yaml

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: test-server-ingress
  annotations:
    kubernetes.io/ingress.class: "ingress-name"
    nginx.ingress.kubernetes.io/backend-protocol: "GRPC"
spec:
  rules:
    - host: hi-space.test.io
      http:
        paths:
          - path: /
            backend:
              serviceName: test-server-service
              servicePort: 9000
  tls:
  - secretName: test-server-tls
    hosts:
      - hi-space.test.io
```

```sh
kubectl apply -f ingress.yaml
```
