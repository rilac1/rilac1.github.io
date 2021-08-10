---
title: [Algorithm]백트래킹으로 순열/조합 구현하기 in Python
# date: 2021-07-05 12:00:00 +/-TTTT
categories: [Algorithm]
tags: [순열, 조합]     # TAG names should always be lowercase
---
알고리즘 문제를 풀다보면 순열과 조합을 사용하여 모든 경우의 수를 구해봐야할 일이 많습니다. (완전탐색 / 브루트포스)  
파이썬에는 `itertools` 라이브러리에 해당 기능을 제공하지만 코딩테스트 환경에서 모든 경우의 수를 미리 만들어놓고 하나하나 독립적으로 처리하면 시간초과 판정을 받을 수 있습니다.  
따라서 **백트래킹 기법**을 사용하여 경우의 수를 동적으로 만들어가면서 변경점 위주로 처리하는 것이 시간측면에서 유리합니다.

---
## 백트래킹 (재귀)
우선 본격적인 코드를 소개하기 전에 완전탐색에 사용되는 백트래킹 기법을 간단하게 소개하겠습니다.
```python
def back(n):
    if n == 탈출조건:
        행동수행    # (ex. print / max / min / etc...)
        return

    변화주기
    back(n+1)      # 재귀호출
    복원하기
```

> 이런 식으로 모든 경우의 수를 차례차례 탐색할 수 있습니다.

---
## 순열 (<sub>n</sub>P<sub>r</sub>)
```python
arr = [1,2,3,4,5]       # n = 5, r = 3
visited = [False] * n

# nPr
def perm():
    if len(picked) == r:
        print(picked)
        return

    for i in range(n):
        if not visited[i]:
            picked.append(arr[i])     # 변화
            visited[i] = True
            perm()                    # 재귀호출
            visited[i] = False
            picked.pop()              # 복원
```
---
## 조합 (<sub>n</sub>C<sub>r</sub>)
```python
arr = [1,2,3,4,5]       # n = 5, r = 3
visited = [False] * n

# nCr
def comb(left):
    if len(picked) == r:
        print(picked)
        return

    for i in range(left, n):   # 조합의 경우 left ~ n까지만 탐색
        picked.append(arr[i])        # 변화
        visited[i] = True
        comb(i)                      # 재귀호출 (마지막에 넣었던 index가 left가 된다.)
        visited[i] = False
        picked.pop()                 # 복원
```


