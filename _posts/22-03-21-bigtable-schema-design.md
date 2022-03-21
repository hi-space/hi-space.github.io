---
title: "[GCP] Bigtable 스키마 디자인"
tags: gcp backend
---

<!--more-->

### Table

- 동일한 테이블에 유사한 스키마가 있는 데이너셋 저장
- Bigtable의 경우 모든 데이터를 하나의 큰 테이블에 저장하는 것이 좋음

### Column Family

- 관련 열을 동일한 column family에 삽입

### Row key

- 데이터 검색에 사용할 쿼리를 기반으로 row key 설계
- 구분된 값 여러 개를 각 row key에 저장
- ex)
	```
	phone#4c410523#20200501
	phone#4c410523#20200502
	tablet#a0b81f74#20200501
	tablet#a0b81f74#20200502
	```

# References

- [https://cloud.google.com/bigtable/docs/schema-design](https://cloud.google.com/bigtable/docs/schema-design)
- [golang-sampels/bigtable](https://github.com/GoogleCloudPlatform/golang-samples/tree/main/bigtable)