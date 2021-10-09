---
title: Python Debugger PDB
tags: python
---

Python은 디버깅을 위해 pdb 라는 Python Debugger 모듈을 제공한다.  
디버깅 하고 싶은 라인에 `breakpoint()` 입력하고 python 실행하면 된다.

|   Command    |           Description            |
| :----------: | :------------------------------: |
|     next     |          다음 라인 실행          |
|   continue   |  다음 breakpoint 영역까지 실행   |
|     step     |         함수 내부로 진입         |
|    return    | 현재 함수의 return 직전까지 실행 |
| !변수명 = 값 |          변수에 값 할당          |
|    print     |           변수값 출력            |
|     list     |       소스코드 리스트 출력       |
|    where     |           콜스택 출력            |
|     help     |              도움말              |

<!--more-->
