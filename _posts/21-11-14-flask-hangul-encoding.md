---
title: Flask 한글 깨짐
tags: python flask troubleshooting
---

## Problem

`pymongo` 에서 데이터를 받아와 json 형태로 response 할 때, utf-8 인코딩이 되지 않아 한글이 깨져서 보임

## Solution

Flask는 기본적으로 ascii 인코딩 형태로 데이터를 출력한다.

### 1. Flask 앱 설정 변경

```py
app = Flask(__name__)
app.config['JSON_AS_ASCII'] = False
```

### 2. json 

```py
def cursor_to_dict(cursor):
    docs_list = list(cursor)
    return json.dumps(docs_list, ensure_ascii=False, default=json_util.default)
```
