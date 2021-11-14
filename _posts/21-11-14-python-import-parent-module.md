---
title: "[Python] parent 모듈 import"
tags: python
---

```py
from inspect import getsourcefile
import os.path as path, sys

current_dir = path.dirname(path.abspath(getsourcefile(lambda:0)))
sys.path.insert(0, current_dir[:current_dir.rfind(path.sep)])
```

<!--more-->

## Reference

- [importing-modules-from-parent-folder](https://stackoverflow.com/questions/714063/importing-modules-from-parent-folder)