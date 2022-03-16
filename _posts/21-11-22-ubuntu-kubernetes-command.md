---
title: "[Ubuntu] kubernetes 설치 & 명령어"
tags: ubuntu env kubernetes backend
---

## Install

```sh
# Ubuntu
sudo apt install apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install kubelet kubeadm kubectl kubernetes-cni

# Mac
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/arm64/kubectl"
# or
brew install kubectl
```

### Check

```sh
kubectl version --client && kubeadm version
```

```sh
alias k='kubectl'
```


<!--more-->

## Commands

#### 현재 클러스터 노드 정보

```sh
kubectl get nodes -o wide

kubectl get pods
```

#### pod 로그

```sh
kubectl logs -f {pod-name}
```

#### pod 접속

```sh
kubectl exec {pod-name} -it bash
```

#### 설정 적용

```sh
kubectl apply -f {}
```

#### 전체 Container 리스트

```sh
kubectl get pods --all-namespaces -o jsonpath="{..image}" |\
tr -s '[[:space:]]' '\n' |\
sort |\
uniq -c
```

#### Container ID, Docker 이미지 확인

```sh
kubectl get pods --all-namespaces -o jsonpath='{range .items[*]}{"pod: "}{.metadata.name}{"\n"}{range .status.containerStatuses[*]}{"\tname: "}{.containerID}{"\n\timage: "}{.image}{"\n"}{end}'
```

# References

- [https://zzsza.github.io/development/2019/01/11/kubernetes-and-deployment/](https://zzsza.github.io/development/2019/01/11/kubernetes-and-deployment/)
- [https://subicura.com/k8s/guide/service.html#service-clusterip-%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5](https://subicura.com/k8s/guide/service.html#service-clusterip-%E1%84%86%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%86%AF%E1%84%80%E1%85%B5)
