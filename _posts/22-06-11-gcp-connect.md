---
title: "GCP VM 인스턴스 접속"
tags: gcp
---

<!--more-->

### 로그인

```sh
gcloud auth login
```

### VM 인스턴스 리스트

```sh
gcloud compute instances list
```

### VM 인스턴스 접근

```sh
gcloud compute ssh --project [PROJECT_ID] --zone [ZONE] [INSTANCE_NAME]
# gcloud compute ssh --zone asia-northeast3-c ev-object-detection
```

---

## 영구 디스크 추가

```sh
# 추가한 디스크 확인
sudo lsblk 

# EXT4로 포맷
sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdb	

# /mnt/disks/sdb로 마운트
sudo mkdir -p /mnt/disks/sdb
sudo mount -o discard,defaults /dev/sdb /mnt/disks/sdb

# 권한 추가
sudo chmod a+w /mnt/disks/sdb

# UUID 확인
sudo blkid /dev/sdb

# sudo vi /etc/fstab에 아래 내용 추가 (부팅시 자동 마운트)
UUID=UUID_VALUE /mnt/disks/sdb ext4 discard,defaults,nobootwait 0 2

# 마운트 확인
sudo df -h
```
