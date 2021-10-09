---
title: Python 하위 폴더 내의 모든 파일 리스트 
tags: python
---

```py
import os
from glob import glob

path = '.'
result = [y for x in os.walk(path) for y in glob(os.path.join(x[0], '*.png'))]
```

<!--more-->

### 특정 파일을 찾고 Rename

```py
import os
from glob import glob

path = '.'
result = [y for x in os.walk(path) for y in glob(os.path.join(x[0], '*.png'))]

for f in result:
    newFile = f.replace('leftImg8bit.png', 'gtFine_labelIds.png')
    os.rename(f, newFile)
```