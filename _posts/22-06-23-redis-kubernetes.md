---
title: "k8s에 Redis 세팅"
tags: k8s
---

<!--more-->

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: redis-config-map
data:
  redis-config: ""

---

apiVersion: v1
kind: Service
metadata:
  name: redis-svc
  labels:
    app: redis
spec:
  selector:
    app: redis
  ports:
  - name: redis
    protocol: TCP
    port: 6379
    targetPort: 6379

---

apiVersion: v1
kind: Pod
metadata:
  name: redis
  labels:
    app: redis
spec:
  containers:
  - name: redis
    image: redis:latest
    command:
      - redis-server
      - "/redis-master/redis.conf"
    env:
    - name: MASTER
      value: "true"
    ports:
    - containerPort: 6379
    resources:
      limits:
        cpu: "0.1"
    volumeMounts:
    - mountPath: /redis-master-data
      name: data
    - mountPath: /redis-master
      name: config
  volumes:
    - name: data
      emptyDir: {}
    - name: config
      configMap:
        name: redis-config-map
        items:
        - key: redis-config
          path: redis.conf

```

config map을 통해 redis 설정하고, 다른 pod에서도 사용 가능하도록 서비스 노출

### References

- [컨피그맵을 사용해서 Redis 설정하기](https://kubernetes.io/ko/docs/tutorials/configuration/configure-redis-using-configmap/)
- [Deploying Redis Cluster on Kubernetes](https://www.containiq.com/post/deploy-redis-cluster-on-kubernetes)
